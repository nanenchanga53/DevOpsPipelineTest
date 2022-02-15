# PipeLine에서 실행한 UnitTest 결과 확인
> pipeline을 구성시 기본 DotNet으로 YAML을 설정시 `task: VSTest@2` 테스크가 있었을 것이다. 이 작업은 비주얼스튜디오 솔루션 파일에 있는 UnitTest 프로젝트를 읽어 테스트를 실행하는 명령이다. PipeLine의 Job을 보면서 CLI화면에서 결과를 확인할 수 있지만 불편한 것을 확인할 수 있다. Pipeline을 구성하면 Test Plans에서 결과들을 확인 가능하다. 여기서는 Test Plans를 만드는걸 설명하지 않는다. 별도 문서에서 작성하겠다. 아래는 Visual Studio에서 제작했을 경우를 기준으로 설명한다.

## 필요 준비물
1. DevOps Pipeline
2. 우선 레포지터리에 Unit Test 프로젝트가 없다면 MSTest 등의 UnitTest 프로젝트와 테스트를 만들어 Repo 레포지터리에 Push를 하자

## UnitTest 결과 확인과정
1. Pipeline 설정을 확인해보자(Pipelines 화면에서 프로젝트를 선택 후 Edit)
2. 이 중 아래와 같은 task가 있는지 확인하자(없다면 빌드 작업 아래에 추가하자)
    ```yml
    - task: VSTest@2
      inputs:
        platform: '$(buildPlatform)'
        configuration: '$(buildConfiguration)'
    ```

3. 위의 설정은 Visual Studio에서 설정한 테스트를 솔루션에서 검색하여 실행하겠다는 의미다.
4. 없었다면 설정을 저장하여 Pipeline을 실행하고 없다면 Run을 클릭해 실행해보자.
5. 그 후 Job 화면으로 들어가 가상머신에서 실행되는 과정을 살펴보자 이중 위와 같다면 VSTest Task에서 Unit Test를 구성하고 실행하는 것을 확인 가능하다.
6. 그 중 가장 아래쪽에 결과를 보면 아래 미지지와 같은 것을 확인 가능할 것이다. `Created test run 숫자` 이런 식의 내용이 있을텐데 테스트 결과를 생성해 `숫자`번 Test로 생성했다는 뜻이다. 이것을 확인하자<br>
![image](https://user-images.githubusercontent.com/39551265/152926811-b4ce7cd7-2cdb-4755-b1e3-fd0344957886.png)

7. Devops 좌측 Test Plans 아래 항목인 Runs로 화면으로 가자<br>![image](https://user-images.githubusercontent.com/39551265/152928432-a737472d-b288-4e05-9dc0-f4046f2ef2c8.png)<br> 
8. 여기서는 실행했었던 UnitTest 들의 결과들이 보인다. 여기서 방금 확인한 Runs `숫자`를 더블클릭하여 자세히 확인하자
9. 아래이미지와 비슷한 화면이 뜰 것이다.<br>![image](https://user-images.githubusercontent.com/39551265/152931069-8a162c8d-de48-455d-a4c5-0db2b7dbf5cb.png)<br>
10. Run summary 탭에서는 실행환경과 결과의 간략한 내용을 확인가능하다. 그리고 Attachments에서 테스트에 대한 테스트 파일을 다운받을 수 있다. 만약 MSTest 등으로 제작하였다면 Visual Studio 에서 열어서 따로 확인할 수 있다.
11. Test Results 탭에서는 각각 테스트에대한 리스트를 볼 수 있다. 자세히 보고싶으면 더블클릭해서 확인하자
12. Filter는 테스트를 필터링 할 수 있는데 만약 테스트개수가 많으면 이름, 실행 시간 등으로 구분해 필터링을 하면된다.
* 만약 모든 Unit테스트를 실행하는 것이 아니라 각각 실행하고 싶으면 경로와 프로젝트를 지정해서 사용해야한다. 위의 방식은 솔루션에서 읽어 모든 Test를 실행하니 좋은 방법이 아니다 그래서 아래와 같이 프로젝트를 별도로 실행하는 것으로 해결이 가능하다. 참고로 .Net FrameWork는 MsBuild 명령어로 실행해야한다.
    ```yml
    - task: DotNetCoreCLI@2
      displayName: 'Run unit tests - $(buildConfiguration)'
      inputs:
        command: 'test'
        arguments: '--no-build --configuration $(buildConfiguration)'
        publishTestResults: true
        projects: '**/프로젝트명.Tests.csproj'
    ```