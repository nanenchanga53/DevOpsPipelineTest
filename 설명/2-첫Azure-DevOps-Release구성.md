# Azure DevOps Pipeline Release 구성
> Azure DevOps 에서 파이프라인을 간단히 구성했으면 이제 배포설정을 해보자 여기서는 자신의 애저 웹 앱에 소유자 권한이 있는 웹앱에 업로드 하는 것을 목표로 한다.

## 준비사항
1. DevOps에서 CI 단계까지 가능한 Azure Pipeline (문서 **1-첫Azure-DevOps-pipeline구성** 참고)
2. Azure Web App(DevOps 게정에서 구독하고 웹앱 관리가 가능한 권한이 있는 상태 - 같은 계정은 쉽게 연결설정이 가능하다. 다른 계정으로 하는 방법은 문서 별첨 - 별도 계정의 Azure Web App 권한 주는 방법** 참조) 여기서는 Net 웹앱 기준으로 설명한다.


## Relase 만들기

1. Pipeline을 설정했던 DevOps에 접속한다.
2. Pipelines를 열고 그 하위 메뉴인 Relases를 선택<br>![image](https://user-images.githubusercontent.com/39551265/152672106-81417c28-c0fc-4704-baae-d498b42acd53.png)<br>
3. +New relase Pipeline 을 눌러 배포 설정을 시작한다.
4. 템플릿 선택에서 Azure App Service deployment를 선택한다.(Azure Web App으로 배포할 경우)<br>![image](https://user-images.githubusercontent.com/39551265/152672845-c587a054-a35b-4336-a605-0b3854134251.png)<br>
5. Stage name 에 배포 단계에 이름을 설정한다.
6. 상단 탭중 Tasks를 선택
7. Parameters에서 배포하려는 Azure Web App 리소스를 소유한 구독과 앱 서비스 이름을 선택한다.
8. Pipeline 탭으로 돌아오자
9. Artifacts에서 +Add an artifact 를 클릭한다.
10. 여기서 Source type은 Pipeline구성을 미리 했었으니 Build를 선택한다.
11. Pipeline을 구성했던 프로젝트와 Source를 선택하고 Add를 클릭(현 단계의 이름을 변경하려면 Source alias를 변경)
12. 상단의 Save를 클릭하면 한번 Relase 작업이 실행될 것이다.
13. 기다리고 난 후 확인하면 배포가 실패할 것이다. 해당 배포설정을 선택후 Logs를 눌러 오류를 확인하자 아래와 같은 오류가 생겼을 것이다.

    `Error: No package found with specified pattern: D:\a\r1\a\**\*.zip<br/>Check if the package mentioned in the task is published as an artifact in the build or a previous stage and downloaded in the current job.`
14. 해당 오류는 빌드 후 배포를 위한 패키지 설정을 Pipeline에서 지정하지 않아 생기는 문제이다. 이를 해결하기위해 **Pipelines**메뉴로 가자
15. 문서 1-첫 Azure-DevOps-pipeline구성에서 만든 Pipeline을 선택한다.
16. 우측상단의 Edit를 클릭해 Yaml을 수정한다.
17. 우측의 Tasks에서 Publish build artifacts를 검색해 추가한다.(이후 설정은 나두고 진행하자)
18. yml파일에 추가한 `task: PublishBuildArtifacts@1` 목록이 있을텐데 가장 아래쪽으로 옮겨주자
19. Save를 하고 Pipeline이 실행되는 걸 기다리자
20. 파이프라인에서 실행이 종료가 되면 Relases화면으로 돌아간 후 새로 생성한 Relase작업으로 가보자 
21. 상단에 +Deploy를 클릭 하여 Deploy Stage를 클릭해 배포를 다시 해보자. 잠시 기다린 후 성공이 되는 것을 확인하자
22. 성공이 완료 됬다면 배포한 Azure Potal의 Azure Web App 리소스에 들어가서 게시한 URL을 확인하여 제대로 배포가 되었는지 확인하자
23. 마지막으로 App 리소스에서 배포 센터에 들어가 DevOps와 제대로 연결됬는지 확인해보자 아래 이미지와 비슷할 것이다.<br>![image](https://user-images.githubusercontent.com/39551265/152721171-b6a4a38d-b307-46ad-90d0-6e11132fa6b6.png)<br>![image](https://user-images.githubusercontent.com/39551265/152721032-dc620edc-3852-4d93-9b16-646449b1df53.png)<br>