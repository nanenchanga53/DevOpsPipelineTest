# 첫 Azure Pipelines Releases 설정
> Pipelines 에서 배포할 수 있도록 설정을 마친 상태에서 Relases화면에서 자주 사용할 법한 기능들을 설명한다. 이 화면은 Relases화면에서 Edit를 클릭해 나온 Edit Relase화면에 들어왔다는 가정하여 설명한다.

## 기능(Artifacts)
1. Pipeline 빌드 후 새 Release를 자동으로 생성
    > Release Pipeline을 처음 생성시 별도 설정을 하지 않았다면 생성하였던 Relases Pipeline의 Release는 한개만 보일 것이다. 하지만 Pipeline을 구성할때 CI쪽은 master에 push가 있을때마다 새로 생성하여 실행하는걸 확인하고 이전기록도 볼 수 있었는데 Releases에서도 똑같이 실행 기록이 남고 확인할 수 있도록 설정한다.
    * Release Pipeline화면에 들어오자 좌측에서 수정하려는 relase pipeline을 선택하고 **Edit**를 클릭해 수정화면으로 들어간다.<br>![image](https://user-images.githubusercontent.com/39551265/152951733-b26950a7-36c3-4238-8102-e1d095eb84c9.png)<br>
    * Artifacts 에서 배포하려고 빌드를 설정한 곳의 번개 표시를 클릭<br>![image](https://user-images.githubusercontent.com/39551265/153154717-1306d47c-e9df-4ec3-9c7a-94daea1e4b62.png)<br>
    * 오른쪽에 Creates a release every time a new build is available 위의 옵션을 Enabled로 변경하자 이것으로 Pipeline에서 build작업이 완료된 것을 확인되면 Relase도 새로 생성하여 실행하게된다.<br>![image](https://user-images.githubusercontent.com/39551265/153155885-5ec44638-c1a2-4121-af4d-ca474eff9934.png)<br>
    * 만약 특정 rops 분기 혹은 그 분기를 선택 하여 사용하자(pipeline 빌드는 2개의 분기에 실행하도록 설정했을때 그 중 한개인 master 분기떄만 생성 등) tag 사용은 가능하면 피하자
2. Pull Request 발생시 Relase를 자동으로 생성
    >보통 공개 프로젝트를 사용하지 않은 DevOps 특성상 잘 사용하지 않은 것이다. 만약 사용한다면 위에 작업처엄 Pull request trigger를 Enabeld로 변경하고 Pull request가 일어나는 분기를 선택하자 물론 미리 Repo에서 Pull Request를 미리 만들어놓아야한다.

3. Relase 생성시간 설정
    > Relase를 지정된 시간에 지속적으로 만들어 싶을 때 사용한다. 보통 Relase는 빌드가 성공 후 테스트 서버에 업로드하는 과정까지 확인하는게 좋아 보통은 사용하지 않을것이다.
    * Arifacts 아래의 시계 클릭<br>![image](https://user-images.githubusercontent.com/39551265/153317421-bbf05d9e-c2b7-4a15-a995-a7c741b4e972.png)<br>
    * Create a new release at the specified times을 Enabled로 변경
    * Relase를 생성할 시간을 설정한다.
    * Only schedule releases if the source or pipeline has changed 설정을 체크해서 원본혹은 파이프라인(현재 수정창)이 변경된 경우에만 생성되도록 설정하자

## 기능(Stages)
1. Stage 추가
    > 배포를 할경우 테스트 서버에 먼저 배포하는 등 한곳만이 아닌 여러곳에 배포하고 싶을 것이다.
    * Stages 에서 + Add 혹은 생성한 Stage 아래에 커서를 옮겨 + Add 를 클릭한다.
    * Stage 아래의 버튼을 클릭하면 Stage 오른쪽 아니면 Stages아래쪽에 새로 생성되었을 것이다.
    * 옆에 생성된 것은 생성 Stage 실행후에 실행한다는 것이고 아래에 생성된건 독립적으로 실행된다는 것이다. 원하는 방식을 선택해 생성하자
    * 문서 2-첫Azure-DevOps-Release구성 에서 구성했던것처럼 새로 생성한 Stage도 설정을 변경하자

2. 배포전 설정(Pre-deployment conditions)
    > 배포전 가능한 설정들을 샬펴본다. Stage의 왼쪽 번개와 사람 아이콘을 클릭해 설정한다.<br>![image](https://user-images.githubusercontent.com/39551265/153323260-d2b50b8b-db3b-4e26-b74d-b219165c14d5.png)<br>

    * Stage 트리거 (Triggers)<br>![image](https://user-images.githubusercontent.com/39551265/153325995-bd2cc5f1-7197-4fe2-a414-48553a7f0c49.png)<br>
        + Select trigger 에서 해당 Stage가 실행될 트리거를 선택한다.
        + After release는 relase실행 후에 실행된다는 의미이며 가장 앞부분에 위치하고 있다면 기본설정으로 되어 있 것다.
        + After Stage는 Stage 실행 후 실행된다는 의미이며 설정하면 해당 Stage가 특정 Stage 오른편으로 이동한다. 
        + Manual Only는 Deploy를 직접 실행할때만 실행하는 설정이다.
        + 그 아래에도 몇가지 설정이 있는데
            + Artifact filters는 특정 아티펙트를 선택해 Task를 실행한다.
            + Schedule는 지정된 시간에 Stage가 실행되도록 설정
            + Pull Request는 pull request를 연결하여 트리거를 설정한다.
        
        
    
    * 배포전 승인(Pre-deployment approvals)<br>![image](https://user-images.githubusercontent.com/39551265/153326148-d81db093-a59f-4584-925e-6c7622465ae7.png)<br>
        
        + Relase에서 특정 사용자의 승인이 있을시 task가 실행되도록 설정한다.
        + Pre-deployment approvals를 Enabled로 변경한다.
        + Approvers 에서 승인을 할 인원을 선택한다.(이름을 검색하면 멤버중에서 선택 가능하다)
        + Timeout을 지정해 승인 제한 시간을 설정한다.
        + Approval policies(승인 정책)에서 더 사용하고 싶은 옵션을 선택하면 된다.

    * Gates 정의 <br>![image](https://user-images.githubusercontent.com/39551265/153339812-b72b3a9c-ec14-44a7-a888-57b24ec58326.png)<br>

        + 우선 게이트를 만들기 전 딜레이시간을 설정한다.(짧게 설정하자)
        + +Add를 클릭한 후 게이트를 만드는 유형을 선택하자(승인 게이트 종류를 추가하는건 DevOps Store에서 추가가 가능하다. 여기서는 기본 제공하는 것들만 간단히 설명한다.)<br>![image](https://user-images.githubusercontent.com/39551265/153341518-606c0b3c-418f-4cc5-be67-b6cc19ef390e.png)<br>
        + Check Azure Policy compliance는 Azure Policy에서 설정된 보안 정책을 검증한다. 접속가능한 구독에 Azure Policy를 제작하고 연결하면 사용가능하다.
        + Azure Function 을 선택하면 Azure Function URL과 KEY를 입력하여 그 결과값을 이용해 통과여부를 결정할 수 있다.
        + Rest API는 Azure Function과 같은 방법으로 사용 가능하다.
        + Azure Monitor는 접속가능한 구독에서 Azure Monitor에 쿼리를 날려 그 결과같을 통해 게이트를 설정 가능하다. 만약 사용빈도가 너무 크거나 하면 사용빈도가 낮은 타이밍에 게이트를 통과시키는 식으로 사용 가능하다.
        + Query items 는 Azure DevOps의 Borads 안에서 저장한 Qaueris를 불러와 그 결과값의 개수 비교한다.

3. 배포 후 조건(Query Work Items)
    > 배포 후에도 조건을 만들 수 있다 그런데 여기서는 승인과 게이트의 사용법은 이미 설명했으니 자동 재배포 트리거만 설명하겠다.
    * 자동 재배포 트리거(Auto-redeploy trigger)
        + 배포가 실패했을시 마지막으로 배포했던 버전으로 다시 배포하는 설정이다. <br>![image](https://user-images.githubusercontent.com/39551265/153343816-8c546e3a-26ec-4a4c-99d8-3640aaf4d962.png)<br>
4. 배포 후 안내 메일 설정
    * 좌측 하단의 Project Settings를 클릭한다.<br>![image](https://user-images.githubusercontent.com/39551265/153344529-8ab3fc40-5ef9-49bd-a236-a48148c94971.png)<br>
    * General중 Notifictions를 클릭한다.<br>![image](https://user-images.githubusercontent.com/39551265/153346792-7923646b-b79e-460b-aa2d-67d98ae7fa4a.png)<br>
    * +New subscription 을 클릭하자<br>![image](https://user-images.githubusercontent.com/39551265/153348067-01bbc3b3-12a9-4327-8713-d5adfaae551f.png)<br>
    * Category 중 Relase를 선택
    * Template 중 A deployment is complted를 선택해 다음으로 넘어가자
    * New Subscription페이지<br>![image](https://user-images.githubusercontent.com/39551265/153349185-3e3e26c4-9a37-439c-8c30-dbc6d0818315.png)<br>
        + Description : 안내메일 발송동작 설정 이름
        + Deliver to : 보낼 대상자 선택 보통은 멤버 아니면 Custom Email Adress를 사용해 특정 사용자에게 보내게 될 것이다.
        + Filter : 어떤 프로젝트에서 일어난 동작인지 설정한다(모든 프로젝트 혹은 특정 프로젝트 한 개)
        + Filter criteria : 발송 조건의 쿼리를 설정한다.
    
    * Finish를 누르고 화면 아래쪽 Relase안에 방금 설정한 이름의 동작이 있는지 확인한다.