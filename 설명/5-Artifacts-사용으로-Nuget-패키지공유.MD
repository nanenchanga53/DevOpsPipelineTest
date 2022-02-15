# Azure Artifacts 사용으로 Nuget 패키지 공유
## Artifacts 설명
* Azure Artifacts는 사설 피드에서 사설 NuGet, npm, Maven 패키지를 관리한다. Visual Studio 또는 Visual Studio Code와 같은 즐겨 찾는 IDE에 이 피드를 통합하고 개발 중에 이 피드에서 패키지를 복원 할 수 있다. 또한 이 피드를 빌드 파이프라인에 통합하고 이 사설 리포지토리에서 패키지를 복원 할 수 있다. Azure Artifacts는 애플리케이션에서 사용중인 공유 공통 패키지가 많고 이러한 교차 절단 속성의 표준 버전을 제어하려는 경우 많은 도움이 된다.

## 목적
* 라이브러리 프로젝트를 만들어 그것을 Nuget 패키지를 만든 후 Artifacts에 패키지를 공유하고 다른 프로젝트 혹은 PC에서 사용이 가능한지 확인한다. 여기서는 Visual Studio를 사용한다.

## 라이브러리 프로젝트 생성
1. 비주얼 스튜디오를 이용해서 라이브러리 프로젝트를 생성하고 적당히 만들어보자, 아래 소스처럼 만들었다 가정하자

```c#
namespace CalcLibrary
{
    public class Calc
    {
        public static double Add(double a, double b)
        {
            return a + b;
        }

        public static double Substract(double a, double b)
        {
            return a - b;
        }

        public static double Multiply(double a, double b)
        {
            return a * b;
        }

        public static double Divide(double a, double b)
        {
            return a / b;
        }        
    }
}
```

## Azure Atrifacts 에서 Feed 생성
1. Azure DevOps에서 Artifacts 메뉴 클릭<br>![image](https://user-images.githubusercontent.com/39551265/153747984-2bacd211-0400-400a-8c3c-8af78d9fc762.png)<br>
2. +Create Feed를 클릭
3. Create new feed에서 각 항목을 채우고 Create를 눌러 생성한다.
    * Name : Feed이름으로 URL이 될 것이니 영어로 하자
    * Visibility : 현재 Feed를 확인할 수 있는 멤버의 범의를 설정
    * Upstream sources : 기본제공 패키지 포함 여부(체크를 해제하면 nuget, npm 등이 비어있다. <span Style="background-color:yellow">여기서는 체크를 해제한다.</span>)
    * Scope : 해당 Feed가 프로젝트 혹은 조직내에서의 속한지 설정한다. 이것의 차이는 [여기](https://docs.microsoft.com/en-us/azure/devops/artifacts/feeds/project-scoped-feeds?view=azure-devops)에서 확인하자
    <br>![image](https://user-images.githubusercontent.com/39551265/153747957-9fab54a7-fffb-46a6-9860-15bd181f46f7.png)<br>

## Feed를 Visual Studio와 연결
1. Connect to feed를 클릭 <br>![image](https://user-images.githubusercontent.com/39551265/153753388-6ef81c82-cbb3-4d2d-a388-d9bde268830d.png)<br>
2. feed에 연결하는 여러가지 종류들이 나오는데 일단 Visial Studio 를 선택하자<br>![image](https://user-images.githubusercontent.com/39551265/153754972-33647024-b9a6-4f54-8a78-7d9cd4ca3e70.png)<br>
3. 새로 생긴 페이지에서 Machine setup 중 Name과 Source를 기억하자 <br>![image](https://user-images.githubusercontent.com/39551265/153756444-8883b26c-6689-4d08-850b-328da360da49.png)<br>
4. Visual Studio 를 켜자
5. 도구 -> Nuget 패키지 관리자 -> 패키지 관리자 설정<br>![image](https://user-images.githubusercontent.com/39551265/153756164-81447956-9bb6-4ae1-b46f-ab2fe418e7ec.png)<br>
6. 패키지 소스를 좌측에서 선택<br>![image](https://user-images.githubusercontent.com/39551265/153756260-360668d3-6774-4cb5-801f-a3f5293f839b.png)<br>
7. 패키지 소스 창에서 <span Style="color:green">초록색 십자버튼</span>을 클릭
8. 새로 생성된 곳의 이름에는 Name, 소스에는 source를 입력하고 **업데이트**를 클릭
9. 도구 -> Nuget 패키지 관리자 -> 솔루션용 Nuget 패키지 관리
10. 우측 상단에 패키지 소스 리스트에서 방금 추가한 이름이 추가되었는지 확인하고 선택한다<br>![image](https://user-images.githubusercontent.com/39551265/153756662-f3c647e6-cf79-49e0-b139-f5078872dfba.png)<br>
11. 찾아보기 탭으로가 현재 찾을 수 있는 패키지가 없는지 확인한다(Upstream sources를 체크해제하지 않았다면 몇가지가 이미 있을것이다.)

## 라이브러리 프로젝트를 Nuget 패키지로 만들기
1. https://www.nuget.org/downloads 이 주소에서 최신 Nuget.exe 파일을 다운받자
2. Nuget.exe를 환경변수에 등록하거나 라이브러리 프로젝트와 같은 폴더에 넣자
3. 일단 프로젝트를 빌드하자
4. 아래의 명령어를 CLI 창에서 실행하자 이때 위치는 프로젝트가 있는 위치다.(환경변수를 등록 안했다면 맨앞에 .\ 추가 필요)
```
nuget.exe pack <프로젝트명>.csproj
```
5. 새로운 파일로 .nupkg 파일이 생성됬을 것이다. 이것으로 nuget 패키지가 생성되었다.
6. 아래의 명령어를 CLI 창에서 실행하자(키 인증 방식은 VSTS(비주얼스튜디오인증) 혹은 az(에저 인증) 등을 사용하거나 직접 키를 입력하자)
```
nuget.exe push -source "Feed이름" -ApiKey <키 인증방식> <새로생성한패키지명.nupkg>
```
7. 패키지가 Feed에 올가간 것을 Azrue DevOps Artifacts에서 확인하자<br>![image](https://user-images.githubusercontent.com/39551265/153760962-75c17fb7-ddb7-40ef-9d6b-efd697f05d9f.png)<br>
8. Visual Studio 에서 도구 -> Nuget 패키지 관리자 -> 솔루션용 Nuget 패키지 관리
9. 우측 상단에 패키지 소스 리스트에서 새로생성한 이름을 선택
10. 찾아보기 탭으로가면 새로 패키지가 추가된 것을 확인한다<br>![image](https://user-images.githubusercontent.com/39551265/153761190-9c944a32-be4b-466e-aeda-f07be3b398bf.png)<br>
11. 다른 PC에서도 Feed를 Visual Studio에 연결하여 패키지가 보이는지 확인한다.

## 패키지 업데이트
1. Visual Studio 솔루션 탐색기에서 프로젝트를 오른쪽 마우스 클릭하고 속성에 들어간다.
2. 패키지 목록에서 패키지 버전을 변경하자(Core 이상 기준)
3. 라이브러리 프로젝트를 Nuget 패키지로 만들기를 4번부터 다시 실행한다.
4. Azure Devops Artifacts에서 패키지 버전이 업데이트 된 것을 확인한다.<br>![image](https://user-images.githubusercontent.com/39551265/153761474-10baa081-edf6-44f1-ab2b-6d8fc8967e46.png)<br>