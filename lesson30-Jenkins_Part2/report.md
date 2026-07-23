# Отчет: Jenkins - Part2

dockerfile - Multi-line String Parameter

```
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src

COPY MyWebApi/MyWebApi.csproj ./MyWebApi/
RUN dotnet restore MyWebApi/MyWebApi.csproj

COPY MyWebApi/ ./MyWebApi/
RUN dotnet publish MyWebApi/MyWebApi.csproj -c Release -o /app/publish --no-restore

FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS final
WORKDIR /app

COPY --from=build /app/publish .

ENV ASPNETCORE_URLS=http://+:8080
EXPOSE 8080

ENTRYPOINT ["dotnet", "MyWebApi.dll"]
```

generate_report - Multi-line String Parameter

```
using System.Net.Http.Json;
using System.Text.Json;
using System.Text.Json.Serialization;

string userArg = args.Length > 0 ? args[0] : "vasia";
int periodArg = args.Length > 1 && int.TryParse(args[1], out var p) ? p : 1;
string formatArg = args.Length > 2 ? args[2] : "txt";
int portArg = args.Length > 3 && int.TryParse(args[3], out var port) ? port : 5050;

await new ReportDSL()
    .WithPort(portArg)
    .FilterByUser(userArg)
    .FilterByPeriod(periodArg)
    .ExportAs(formatArg)
    .ExecuteAsync();

public class ReportDSL 
{
    private string _user = "";
    private int _period;
    private string _format = "txt";
    private int _port = 5050;

    public ReportDSL WithPort(int port) { _port = port; return this; }
    public ReportDSL FilterByUser(string user) { _user = user; return this; }
    public ReportDSL FilterByPeriod(int period) { _period = period; return this; }
    public ReportDSL ExportAs(string format) { _format = format.ToLower(); return this; }

    public async Task ExecuteAsync() 
    {
        if (string.IsNullOrWhiteSpace(_user)) {
            Console.WriteLine("Error: User is not specified!");
            Environment.Exit(1);
        }

        Console.WriteLine($"Starting report generation for {_user} and period {_period} using port {_port}...");

        using var client = new HttpClient();
        try {
            var response = await client.GetAsync($"http://localhost:{_port}/api/report");
            if (!response.IsSuccessStatusCode) {
                Console.WriteLine($"API Error: Server returned status code {response.StatusCode}");
                Environment.Exit(1);
            }

            var allData = await response.Content.ReadFromJsonAsync(SourceContext.Default.RecordDtoArray);

            if (allData == null || !allData.Any()) {
                Console.WriteLine("DB Error: Database is empty.");
                Environment.Exit(1);
            }

            var filtered = allData
                .Where(x => x.User.Equals(_user, StringComparison.OrdinalIgnoreCase) && x.Period == _period)
                .ToList();

            if (!filtered.Any()) {
                Console.WriteLine($"Error: Records for user {_user} and period {_period} not found.");
                Environment.Exit(1);
            }

            string reportContent = _format == "md" ? BuildMarkdown(filtered) : BuildText(filtered);
            string filename = $"report.{_format}";
            await File.WriteAllTextAsync(filename, reportContent);

            Console.WriteLine("\n=== GENERATED REPORT ===");
            Console.WriteLine(reportContent);
            Console.WriteLine($"Report successfully saved to: {filename}");
        }
        catch (Exception ex) {
            Console.WriteLine($"Critical Execution Error: {ex.Message}");
            Environment.Exit(1);
        }
    }

    private string BuildText(List<RecordDto> data) =>
        $"=== REPORT: {_user.ToUpper()} ===\n" + 
        string.Join("\n", data.Select(x => $"- Period: {x.Period}, Amount: {x.Amount}")) + 
        $"\nTotal: {data.Sum(x => x.Amount)}";

    private string BuildMarkdown(List<RecordDto> data) =>
        $"# Report for {_user}\n\n| Period | Amount |\n|---|---|\n" + 
        string.Join("\n", data.Select(x => $"| {x.Period} | {x.Amount} |")) + 
        $"\n\n**Total:** `{data.Sum(x => x.Amount)}`";
}

public class RecordDto {
    [JsonPropertyName("user")] public string User { get; set; } = "";
    [JsonPropertyName("period")] public int Period { get; set; }
    [JsonPropertyName("amount")] public decimal Amount { get; set; }
}

[JsonSerializable(typeof(RecordDto[]))]
internal partial class SourceContext : JsonSerializerContext
{
}

```

Pipeline script

```
pipeline {
    agent any

    parameters {
        string(name: 'REPORT_USER', defaultValue: 'vasia')
        string(name: 'REPORT_PERIOD', defaultValue: '1')
        choice(name: 'REPORT_FORMAT', choices: ['txt', 'md'])
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                echo '=== Stage 1: Download code ==='
                git branch: 'main', url: 'https://github.com/liix/SimpleApp'
            }
        }

        stage('Restore Dependencies') {
            steps {
                echo '=== Stage 2: Restore Dependencies ==='
                sh 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                echo '=== Stage 3: Build ==='
                sh 'dotnet build --no-restore -c Release'
            }
        }

        stage('Test') {
            steps {
                echo '=== Stage 4: Tests ==='
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    sh 'dotnet test --no-build -c Release'
                }
            }
        }

        stage('Deploy (Publish)') {
            steps {
                echo '=== Stage 5: Deploy ==='
                writeFile file: 'Dockerfile', text: params.dockerfile
                sh """ 
                    docker build -t my-web-api .
                    docker stop my-web-api || true
                    docker rm my-web-api || true
                    docker run -d --name my-web-api -p ${port}:8080 my-web-api
                """
                
                echo 'The App is ready'
            }
        }
        
        stage('Check application') {
            steps {
                sh """curl http://localhost:${port}"""
            }
        }
        
        stage('Execute DSL') {
            steps {
                echo '=== Stage 5: Generate report ==='
                writeFile file: 'generate_report.cs', text: params.generate_report
                sh "dotnet run generate_report.cs -- ${params.REPORT_USER} ${params.REPORT_PERIOD} ${params.REPORT_FORMAT} ${port}"
            }
        }
    }

    post {
        always {
            echo 'Cleaning...'
            cleanWs()
        }
        success {
            echo 'Pipeline finished successfully'
        }
        failure {
            echo 'The build failed!'
        }
        unstable {
            echo 'The build is unstable!'
        }
    }
}


```

Output

```
Started by user student
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins in /var/lib/jenkins/workspace/Main/docker
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Checkout Code)
[Pipeline] echo
=== Stage 1: Download code ===
[Pipeline] git
The recommended git tool is: NONE
No credentials specified
Cloning the remote Git repository
Cloning repository https://github.com/liix/SimpleApp
 > git init /var/lib/jenkins/workspace/Main/docker # timeout=10
Fetching upstream changes from https://github.com/liix/SimpleApp
 > git --version # timeout=10
 > git --version # 'git version 2.43.0'
 > git fetch --tags --force --progress -- https://github.com/liix/SimpleApp +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git config remote.origin.url https://github.com/liix/SimpleApp # timeout=10
 > git config --add remote.origin.fetch +refs/heads/*:refs/remotes/origin/* # timeout=10
Avoid second fetch
 > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
Checking out Revision c42f5d6afca1d9f5fd415d402ea60d93d3fb8a98 (refs/remotes/origin/main)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f c42f5d6afca1d9f5fd415d402ea60d93d3fb8a98 # timeout=10
 > git branch -a -v --no-abbrev # timeout=10
 > git checkout -b main c42f5d6afca1d9f5fd415d402ea60d93d3fb8a98 # timeout=10
Commit message: "add report request"
 > git rev-list --no-walk c42f5d6afca1d9f5fd415d402ea60d93d3fb8a98 # timeout=10
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Restore Dependencies)
[Pipeline] echo
=== Stage 2: Restore Dependencies ===
[Pipeline] sh
+ dotnet restore
  Determining projects to restore...
  Restored /var/lib/jenkins/workspace/Main/docker/MyWebApi/MyWebApi.csproj (in 136 ms).
  Restored /var/lib/jenkins/workspace/Main/docker/MyWebApi.Tests/MyWebApi.Tests.csproj (in 950 ms).
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build)
[Pipeline] echo
=== Stage 3: Build ===
[Pipeline] sh
+ dotnet build --no-restore -c Release
  MyWebApi -> /var/lib/jenkins/workspace/Main/docker/MyWebApi/bin/Release/net10.0/MyWebApi.dll
  MyWebApi.Tests -> /var/lib/jenkins/workspace/Main/docker/MyWebApi.Tests/bin/Release/net10.0/MyWebApi.Tests.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:06.12
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Test)
[Pipeline] echo
=== Stage 4: Tests ===
[Pipeline] catchError
[Pipeline] {
[Pipeline] sh
+ dotnet test --no-build -c Release
Test run for /var/lib/jenkins/workspace/Main/docker/MyWebApi.Tests/bin/Release/net10.0/MyWebApi.Tests.dll (.NETCoreApp,Version=v10.0)
VSTest version 18.0.2 (x64)

Starting test execution, please wait...
A total of 1 test files matched the specified pattern.

Passed!  - Failed:     0, Passed:     1, Skipped:     0, Total:     1, Duration: 9 ms - MyWebApi.Tests.dll (net10.0)
[Pipeline] }
[Pipeline] // catchError
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Deploy (Publish))
[Pipeline] echo
=== Stage 5: Deploy ===
[Pipeline] writeFile
[Pipeline] sh
+ docker build -t my-web-api .
#0 building with "default" instance using docker driver

#1 [internal] load build definition from Dockerfile
#1 transferring dockerfile: 483B done
#1 DONE 0.0s

#2 [internal] load metadata for mcr.microsoft.com/dotnet/sdk:10.0
#2 ...

#3 [internal] load metadata for mcr.microsoft.com/dotnet/aspnet:10.0
#3 DONE 0.2s

#2 [internal] load metadata for mcr.microsoft.com/dotnet/sdk:10.0
#2 DONE 0.2s

#4 [internal] load .dockerignore
#4 transferring context: 2B done
#4 DONE 0.0s

#5 [final 1/3] FROM mcr.microsoft.com/dotnet/aspnet:10.0@sha256:1fa23fc4872d95fd71c2833ebe65d7e84a43b2d51a31d119516852f13d9505a7
#5 resolve mcr.microsoft.com/dotnet/aspnet:10.0@sha256:1fa23fc4872d95fd71c2833ebe65d7e84a43b2d51a31d119516852f13d9505a7 0.0s done
#5 DONE 0.0s

#6 [build 1/6] FROM mcr.microsoft.com/dotnet/sdk:10.0@sha256:ed034a8bf0b24ded0cbbac07e17825d8e9ebfe21e308191d0f7421eaf5ad4664
#6 resolve mcr.microsoft.com/dotnet/sdk:10.0@sha256:ed034a8bf0b24ded0cbbac07e17825d8e9ebfe21e308191d0f7421eaf5ad4664 0.0s done
#6 DONE 0.0s

#7 [internal] load build context
#7 transferring context: 306.50kB 0.0s done
#7 DONE 0.0s

#8 [build 2/6] WORKDIR /src
#8 CACHED

#9 [build 3/6] COPY MyWebApi/MyWebApi.csproj ./MyWebApi/
#9 CACHED

#10 [build 4/6] RUN dotnet restore MyWebApi/MyWebApi.csproj
#10 CACHED

#11 [build 5/6] COPY MyWebApi/ ./MyWebApi/
#11 DONE 0.0s

#12 [build 6/6] RUN dotnet publish MyWebApi/MyWebApi.csproj -c Release -o /app/publish --no-restore
#12 3.962   MyWebApi -> /src/MyWebApi/bin/Release/net10.0/MyWebApi.dll
#12 4.003   MyWebApi -> /app/publish/
#12 DONE 4.1s

#13 [final 2/3] WORKDIR /app
#13 CACHED

#14 [final 3/3] COPY --from=build /app/publish .
#14 CACHED

#15 exporting to image
#15 exporting layers done
#15 exporting manifest sha256:5b0ae5015afc3684b0877dee29818e091a411598cc73174b1fd73d4f17ba0541 done
#15 exporting config sha256:a1ca078486bb6ea4319512195abecc996f69ef1950147ca5258d8c1b46830e0d done
#15 exporting attestation manifest sha256:55afa34d2f89826bcad9da1fbc9a9b216293047f889e0f13ce63ee01c6ff2a00 0.0s done
#15 exporting manifest list sha256:43d9673e89e2f39b8ca3475a33170100d8b6e708c2dbc80b72757b0279af7909 done
#15 naming to docker.io/library/my-web-api:latest done
#15 unpacking to docker.io/library/my-web-api:latest done
#15 DONE 0.1s
+ docker stop my-web-api
my-web-api
+ docker rm my-web-api
my-web-api
+ docker run -d --name my-web-api -p 5050:8080 my-web-api
2255a69ade018c0d7f126a7205dbd37f913560f9eefa598f7fcf63972f7c461d
[Pipeline] echo
The App is ready
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Check application)
[Pipeline] sh
+ curl http://localhost:5050
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed

  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100    12    0    12    0     0     98      0 --:--:-- --:--:-- --:--:--    98
Hello World!
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Execute DSL)
[Pipeline] echo
=== Stage 5: Generate report ===
[Pipeline] writeFile
[Pipeline] sh
+ dotnet run generate_report.cs -- vasia 1 txt 5050
Starting report generation for vasia and period 1 using port 5050...

=== GENERATED REPORT ===
=== REPORT: VASIA ===
- Period: 1, Amount: 30
- Period: 1, Amount: 70
Total: 100
Report successfully saved to: report.txt
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Declarative: Post Actions)
[Pipeline] echo
Cleaning...
[Pipeline] cleanWs
[WS-CLEANUP] Deleting project workspace...
[WS-CLEANUP] Deferred wipeout is used...
[WS-CLEANUP] done
[Pipeline] echo
Pipeline finished successfully
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS

```