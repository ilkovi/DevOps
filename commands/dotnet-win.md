
### Get SDKs
dotnet --list-sdks



### Build

dotnet restore -r win-x64 /p:PublishTrimmed=true

dotnet build --runtime win-x64

dotnet publish --self-contained -r win-x64

`In the conf
 'restore_opt'  : '--no-cache -r win-x64 /p:PublishTrimmed=true',
 'publish_opt'  : '--no-restore --self-contained -r win-x64',



### RUN dotnet on Windows

dotnet service.dll --environment Production --http://*:4443
dotnet service.dll --environment Development --urls="http://*:4443


### Generate certificate Windows

dotnet dev-certs https


### Create Service from EXE

SC CREATE "Service Name" binpath= "C:\full_path_to.exe"



### Linux env variables

ASPNETCORE_URLS "http://*:1010;https://*:1020"
ASPNETCORE_ENVIRONMENT=Development



