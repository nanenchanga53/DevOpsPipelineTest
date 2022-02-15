# 별도 계정의 Azure WebApp 권한 주는 방법
> 여기서는 Azure 기본개념인 구독에 대해 설명하지는 않고 Azure Devops서 Pipeline 구성시 WebApp에 연결하기 위해 간단히 하는 방법만 설명할 것이다. 이 설명은 Azure Active Directory 기초개념이니 [AZ-900](https://docs.microsoft.com/ko-kr/learn/certifications/exams/az-900)을 공부하는게 도움이 될 것이다.

## 목적
> Azure Devops의 Pipeline 에서 Release 구성을 만들 떄 현제 DevOps의 계정이 아닌 별도 계정의 Azure Web Apps와 연결하고 싶을 것이다. 별도의 설정이 없이는 현제 계정의 Azure WebApp 의 계정만이 나올 것이다. 이것을 해결하려면 Web App을 소유하고 있는 계정에서 권한을 부여해야 한다.

## Azure DevOps PipeLine에서 접속 가능하도록 권한 설정
 
### 문제점 확인
1. Azure DevOps에서 Release를 구성시 Azure App service deployment를 선택해서 나오는 Task 설정에서 구독 리스트상자를 선택하면 자신의 구독밖에 선택할 수 없어서 다른 구독에서 생성한 WebApp은 선택할 수 없을 것이다.<br>![image](https://user-images.githubusercontent.com/39551265/152629859-6803b6fb-76ba-4234-9806-f9024d556b6a.png)<br>![image](https://user-images.githubusercontent.com/39551265/152629780-f09f0290-7289-437d-b4a0-9ea881514454.png)<br>

### Azure Active Directory 사용자추가
1. 현재 사용하려는 DevOps의 계정이 아닌 Web App을 소유하고 있는 계정으로 [Azure Potal](https://portal.azure.com/)에 접속한다.
2. 접속한 계정에서 **Azure Active Directory** 에 들어간다.<br>
![image](https://user-images.githubusercontent.com/39551265/152631166-ee15e511-e90d-4920-82d7-e13d80754872.png)
<br>

3. AD 페이지에서 좌측에 **사용자**를 찾아 클릭한다.<br>
![image](https://user-images.githubusercontent.com/39551265/152631204-79f2d68b-ae76-4b57-8be4-258b335e8235.png)<br>

4. 사용자 페이지로 들어오면 현제 계정에 관련해 등록된 리스트가 보인다.
    > 여기서 만약 Azure DevOps에서 Pipeline을 구성하는 계정이 있다면 그 계정을 선택해서 아래 **권한 추가**로 넘어가자

5. +새 게스트 사용자를 클릭<br>
![image](https://user-images.githubusercontent.com/39551265/152642992-f14f7a4e-ac8b-4d89-9af1-6dc8f9d95bd7.png)<br>

6. **새 사용자** 페이지에서 **ID** 에 관리할 이름과 DevOps를 관리하고 있는 메일주소를 적고 **초대**를 클릭한다.<br>
![image](https://user-images.githubusercontent.com/39551265/152645073-b7cd2084-ac4a-466f-b828-fc08a3bcd65f.png)<br>

7. DevOps 계정의 이메일에 초대메일이 도착했을 것이다. 이것을 열어 연결 URL을 클릭하면 초대가 완료된다.

### 권한 추가

1. 사용자 페이지에서 DevOps 계정을 가지고 있는 사용자를 클릭한다.
2. 관리 목록 중 **할당된 역할**을 클릭한다.<br>![image](https://user-images.githubusercontent.com/39551265/152645807-2b8b8770-06f3-4409-8a7c-3d9aec2e151f.png)<br>
3. **+할당 추가**를 클릭<br>![image](https://user-images.githubusercontent.com/39551265/152645831-695cbdd5-1080-4ef2-aa9f-f85d3afe4ded.png)<br>
4. 여러가지 역할에 대한 리스트가 존재한다. [여기](https://docs.microsoft.com/ko-kr/azure/role-based-access-control/rbac-and-directory-admin-roles?context=/azure/active-directory/roles/context/ugr-context)서 필요한 역할을 찾아 할당 할 수 있지만 지금은 모든 권한을 줄 수 있는 **전역 관리자**를 선택하고 추가한다.<br>
![image](https://user-images.githubusercontent.com/39551265/152645884-946fb823-4a31-49d1-95b1-fbf9540a5997.png)
<br>

### 구독 역할 할당 추가

1. Azure Potal 메인 페이지로 돌아간다.<br>![image](https://user-images.githubusercontent.com/39551265/152647201-a1b73247-8191-4b1d-a245-ce5277556d48.png)<br>
2. 구독을 클릭하고 웹앱을 관리하고 있는 구독을 선택한다.
3. **엑세스 제어(IAM)** 클릭<br>![image](https://user-images.githubusercontent.com/39551265/152647201-a1b73247-8191-4b1d-a245-ce5277556d48.png)<br>
4. **+추가** -> **역할 할당 추가**<br>![image](https://user-images.githubusercontent.com/39551265/152647295-8bffd72d-c101-4969-aac5-2a8190428fd4.png)<br>
5. 역할 탭에서 할당 받으려는 계정의 역할을 선택한다.(모든 권한을 주려면 소유자를 선택) 다음을 클릭
6. 구성원에 탭에서 **+구성원 선택**을 클릭하고 DevOps 계정을 가지고 있는 사용자를 선택한다.
7. **검토+할당** 을 클릭해 구독을 할당할 수 있다.

### 웹앱 역할 할당 추가

1. Azure Potal 메인 페이지로 돌아간다.
2. 배포를 하려는 웹 앱 리소스로 들어간다.
3. **엑세스 제어(IAM)** 클릭<br>![image](https://user-images.githubusercontent.com/39551265/152647201-a1b73247-8191-4b1d-a245-ce5277556d48.png)<br>
4. **+추가** -> **역할 할당 추가**<br>![image](https://user-images.githubusercontent.com/39551265/152647295-8bffd72d-c101-4969-aac5-2a8190428fd4.png)<br>
5. 역할 탭에서 할당 받으려는 계정의 역할을 선택한다.(모든 권한을 주려면 소유자를 선택) 다음을 클릭
6. 구성원에 탭에서 **+구성원 선택**을 클릭하고 DevOps 계정을 가지고 있는 사용자를 선택한다.
7. **검토+할당** 을 클릭해 해당 웹 앱에 대한 역할을 할당할 수 있다.

### DevOps Pipelise Relase에서 확인
1. Azure DevOps에서 Release를 구성시 Azure App service deployment를 선택해서 나오는 Task 설정에서 구독 리스트상자를 선택하면 할당받은 구독을 선택이 리스트에 추가된 것을 확인할 수 있다. 그후 App name 리스트에 역할이 추가된 이름이 추가된 것을 확인이 가능하다. 그것을 선택해 Relase를 구성하면 된다.<br>![image](https://user-images.githubusercontent.com/39551265/152629780-f09f0290-7289-437d-b4a0-9ea881514454.png)<br>