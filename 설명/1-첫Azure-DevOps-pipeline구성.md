# 첫 DevOps 파이프라인 구성
> 애저에서 프로젝트 파이프라인 구성을 시작해보자. 여기서는 CI : 지속적 통합(Continuous Integration) 만을 사용한다. CD 쪽은 애저 DevOps의 경우 Publish로 따로 나뉘어져 있으며 관리도 그쪽에서 한다. 이 문서에서는 웹앱을 커밋했을때 제대로 빌드가 되는지 확인하는 것 까지만을 적는다. 별도 문서에서 Unit Test 추가등을 다룰 것이다.

## 준비사항
1. **Azure Dev Ops 계정** - [여기서](https://azure.microsoft.com/ko-kr/) 만들 수 있다. 만드는 방법까지는 [rickman2 블로그](https://m.blog.naver.com/rickman2/222014617568)를 참고하자
2. **웹 프로젝트** - 여기서는 .Net 웹앱 프로젝트를 사용한다.
3. **Repos** - Azure Devops의 Repos를 이용해 소스를 관리한다. 여기에 준비한 웹앱 프로젝트를 업로드한다.


## PipeLine 만들기
1. 자신의 Devops에서 좌측 메뉴 중 **PipeLines**를 클릭<br>
![image](https://user-images.githubusercontent.com/39551265/152267129-7c2ec1b1-020c-4a7f-84bf-ce97c2828f40.png)<br>

2. 우측 상단의 **New pipeline** 클릭<br>
![image](https://user-images.githubusercontent.com/39551265/152267194-a248d554-f335-467e-a1f2-478c3922d505.png)<br>

3. 이제 Conncet 페이지가 열릴것이다. 이곳에서 자신의 프로젝트 소스가 있는 위치를 선택하면 되는데 여기서는 **Azure Repos Git**을 고른다.(만약 Git에서 소스를 관리하면 GitHub를 선택)<br>
![image](https://user-images.githubusercontent.com/39551265/152267569-bcfce572-f0be-46fb-a92e-0532a8f1057a.png)<br>

4. Selct 페이지로 전환되었다. 여기서 관리를 원하는 레보지토리를 선택한다.

5. Configure 페이지로 전환되었다. 여기서 CI 구성을 위한 설정을 선택할 수 있는데 이번 문서에서 사용하는건 .Net 프로젝트이니 **.Net 을 선택**(.net core 프로젝트를 선택해도 yml구성은 같다. 설정등에서 문제가 있을 수 있으니 Core이하 프로젝트는 .Net core를 선택)

6. Review 페이지로 전환되었다. 여기서 CI를 구성하는 yml파일이 제대로 구성되었는지 확인하고 수정이필요하면 수정한다. (오른쪽에 자신의 원하는 설정을 추가하기위한 추천목록이 있다. 이것을 클릭해 YML파일을 수정하거나 할 수 있는데 자세한 것은 [공식문서에서 확인](https://docs.microsoft.com/ko-kr/azure/devops/pipelines/yaml-schema/?view=azure-pipelines)하자)

7. 수정한 부분이 없다면 아래와 같을 것이다. 아래는 master에 push가 있을시 윈도우 가상머신에서 프로젝트 솔루션을 검색해 빌드하고 실행에 문제가 없는지 테스트하는 과정을 설정한다.
(참고로 .Net6는 아래방식으로 실행을 하지 못한다. 가상머신의 VS버전이 아직 2019라 빌드가 안되서 Global Using 등의 기능을 사용하지 못하기 때문이다 완전 최신기능을 빼서 따로 빌드버전을 설정하든지 아니면 MSBuild가 아니라 dotNetBuild 명령어를 사용해야한다. [참고사이트](https://subhankarsarkar.com/dot-net6-azure-web-app-deployment-using-azure-devops-pipeline/))
```yml
# ASP.NET
# Build and test ASP.NET projects.
# Add steps that publish symbols, save build artifacts, deploy, and more:
# https://docs.microsoft.com/azure/devops/pipelines/apps/aspnet/build-aspnet-4

trigger:
- master

pool:
  vmImage: 'windows-latest'

variables:
  solution: '**/*.sln'
  buildPlatform: 'Any CPU'
  buildConfiguration: 'Release'

steps:
- task: NuGetToolInstaller@1

- task: NuGetCommand@2
  inputs:
    restoreSolution: '$(solution)'

- task: VSBuild@1
  inputs:
    solution: '$(solution)'
    msbuildArgs: '/p:DeployOnBuild=true /p:WebPublishMethod=Package /p:PackageAsSingleFile=true /p:SkipInvalidConfigurations=true /p:PackageLocation="$(build.artifactStagingDirectory)"'
    platform: '$(buildPlatform)'
    configuration: '$(buildConfiguration)'

- task: VSTest@2
  inputs:
    platform: '$(buildPlatform)'
    configuration: '$(buildConfiguration)'

```

8. 이제 Save and run을 클릭<br>
![image](https://user-images.githubusercontent.com/39551265/152270037-ddd32687-a14f-4796-91eb-81d97dd16c5d.png)<br> 

9. 새로나온 페이지에서 커밋내용을 입력하고 저장한다. 이제 PipeLine이 생성되었을 것이다. 그리고 ropos(리포지토리)를 확인해 보면 최상단위치에 yml파일이 새로 생성되었을 것이다.

10. Pipelines 페이지로 돌아오자 여기서 우선 자신의 레포지토리를 클릭하여 Pipeline에서 실행 상태를 확인한다.

11. runs탭으로 넘어갔을 것이다 이곳에서는 가상머신에서 CI에서 yaml 설정대로 실행했던 전적이 Commit 이름 리스트로 쌓인다. 새로생성된 것을 클릭해보자. 문제가 없다면 아래 이미지와 비슷할 것이다.<br>
![image](https://user-images.githubusercontent.com/39551265/152283029-dc1f2be8-ed78-4507-b3c1-50f9200de9c4.png)
<br>

12. 어떻게 실행했었는지는 직접 클릭해 보면 CLI창이 뜨면서 CI과정을 볼 수 있다. 만약 소스코드의 문제가 있어 빌드 할 수 없는 코드를 push했다면 이 CLI창에서 에러를 보여줄 것이다. 이것을 확인하여 오류를 고쳐나가면 된다.
