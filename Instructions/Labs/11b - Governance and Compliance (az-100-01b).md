﻿---
랩:
    제목: '거버넌스 구현 및 Azure 이니셔티브 및 리소스 잠금 준수'
    모듈: 'Azure 구독 및 리소스 관리'
---

# 랩: 거버넌스 구현 및 Azure 이니셔티브 및 리소스 잠금 준수

이 랩의 모든 작업은 Azure 포털(PowerShell 클라우드 셸 세션 포함)에서 수행됩니다.  

   > **참고**: Cloud Shell을 사용하지 않는 경우 랩 가상 시스템에 Azure PowerShell 1.2.0 모듈(또는 최신 모듈)이 설치되어 있어야 합니다.[https://docs.microsoft.com/ko-kr/powershell/azure/install-az-ps?view=azps-1.2.0](https://docs.microsoft.com/ko-kr/powershell/azure/install-az-ps?view=azps-1.2.0) 

랩 파일: 

-  **Allfiles/Labfiles/AZ-100.1/az-100-01b_azuredeploy.json**

-  **Allfiles/Labfiles/AZ-100.1/az-100-01b_azuredeploy.parameters.json**

### 시나리오
  
Adatum Corporation은 Azure 구독에서 리소스 태그 지정을 적용하기 위해 Azure 정책 및 이니셔티브를 사용하려고 합니다. 환경이 규정을 준수하면 Adatum은 리소스 잠금을 구현하여 의도하지 않은 변경을 방지하려고 합니다.

### 목표
  
이 과정을 완료하면 다음과 같은 역량을 갖추게 됩니다:

-  Azure 정책 및 이니셔티브를 사용하여 Azure 태그 구현

-  Azure 리소스 잠금 구현


### 연습 1: Azure 정책 및 이니셔티브를 사용하여 Azure 태그 구현

이 연습의 주요 작업은 다음과 같습니다:

1. Azure 리소스 관리자 템플릿을 사용하여 Azure 리소스를 프로비전합니다.

1. 리소스 태그 지정 준수를 평가하는 이니셔티브 및 정책을 구현합니다.

1. 리소스 태그 지정 준수를 적용하는 정책을 구현합니다.

1. 태그 지정 적용 및 태그 지정 준수를 평가합니다.

1. 리소스 태그 지정 비준수에 대한 수정을 구현합니다.

1. 수정 작업이 규정 준수에 미치는 영향을 평가합니다.


#### 작업 1: Azure 리소스 관리자 템플릿을 사용하여 Azure 리소스를 프로비전합니다.

1. 랩 가상 컴퓨터에서 Microsoft Edge를 시작하고 [**http://portal.azure.com**](http://portal.azure.com) 에서 Azure 포털을 찾아보고 이 랩에서 사용하려는 Azure 구독에서 소유자 역할이 있는 Microsoft 계정을 사용하여 로그인합니다.

1. Azure 포털에서 **새** 블레이드로이동합니다.

1. **새** 블레이드에서 Azure 마켓플레이스에서 **템플릿 배포** 를 검색합니다.

1. 검색 결과 목록을 사용하여 **사용자 지정** 배포 블레이드로 이동합니다.

1. **사용자 지정 배포** 블레이드에서 **편집기에서 사용자 고유의 템플릿 빌드** 를 선택합니다.

1. **편집 템플릿** 블레이드에서 템플릿 파일 **az-100-01b_azuredeploy.json** 을 로드합니다. 

   > **참고**: 템플릿의 내용을 검토하고 일부 리소스에 대한 태그를 포함하여 Windows Server 2016 데이터 센터를 호스팅하는 Azure VM의 배포를 정의합니다.

1. 템플릿을 저장하고 **사용자 지정 배포** 블레이드로 돌아갑니다. 

1. **사용자 지정 배포 블레이드** 에서 **매개 변수 편집** 블레이드로 이동합니다.

1. **편집 매개 변수** 블레이드에서 매개 변수 파일 **az-100-01b_azuredeploy.parameters.json** 을 로드합니다. 

1. 매개 변수를 저장하고 **사용자 지정 배포** 블레이드로 돌아갑니다. 

1. **사용자 지정 배포** 블레이드에서 다음 설정을 사용하고 템플릿 배포를 시작합니다:

    - 구독: 이 랩에서 사용 중인 구독의 이름

    - 리소스 그룹: 새 리소스 그룹 **az1000101b-RG** 의 이름

    - 위치: 랩 위치에 가장 가까운 Azure 지역의 이름 및 Azure VM을 프로비전할 수 있는 위치

    - Vm 크기: **Standard_DS1_v2**

    - Vm 이름: **az1000101b-vm1**

    - 관리자 사용자 이름: **학생**

    - 관리자 암호: **Pa55w.rd1234**

    - 가상 네트워크 이름: **az1000101b-vnet1**

    - 환경 이름: **랩**

   > **참고**: Azure VM을 프로비전할 수 있는 Azure 지역을 식별하려면 [**https://azure.microsoft.com/ko-kr/regions/offers/**](https://azure.microsoft.com/ko-kr/regions/offers/)참고하십시오.

   > **참고**: 다음 단계로 진행하기 전에 배포가 완료될 때까지 기다리지 마십시오.
   
1. Azure 포털에서 **태그** 블레이드로 이동합니다.

1. **태그** 블레이드에서 **환경** 태그를 값 **랩** 으로 설정하여 모든 리소스를 표시합니다. 이전 작업에 배포된 리소스 중 일부만 이 태그가 할당됩니다.

   > **참고**: 이 시점에서 일부 리소스만 프로비전되었지만 태그가 할당되지 않은 경우 최소한 몇 개만 표시해야 합니다.


#### 작업 2: 리소스 태그 지정 준수를 평가하는 정책 및 이니셔티브를 구현합니다.

1. Azure 포털에서 **정책** 블레이드로 이동합니다.

1. **정책 블레이드** 에서 **정책 - 정의** 블레이드로 이동합니다.

1. **정책 정의** 블레이드에서 **적용 태그 및 해당 값 정책** 정의를 표시합니다.

1. **Tag 및 기본값** 정책 정의 블레이드에서정의 복제 기능을 사용하여 다음 설정을 사용하여 새 정책을 만듭니다:

    - 정의 위치: 이 랩에서 사용 중인 구독의 이름

    - 이름: **az10001b - 감사태그 및 그 값**

    - 설명: **필요한 태그와 그 값을 감사합니다. 리소스 그룹에는 적용되지 않습니다.**

    - 범주: 새 범주 **랩의** 이름

    - 정책 규칙: 정책 정의에 다음 콘텐츠가 있도록 **감사로 설정된** **효과가** 있는 기존 정책 규칙:

   ```
   {
     "mode": "indexed",
     "policyRule": {
       "if": {
         "not": {
           "field": "[concat('tags[', parameters('tagName'), ']')]",
           "equals": "[parameters('tagValue')]"
         }
       },
       "then": {
         "effect": "audit"
       }
     },
     "parameters": {
       "tagName": {
         "type": "String",
         "metadata": {
           "displayName": "Tag Name",
           "description": "Name of the tag, such as 'environment'"
         }
       },
       "tagValue": {
         "type": "String",
         "metadata": {
           "displayName": "Tag Value",
           "description": "Value of the tag, such as 'production'"
         }
       }
     }
   }
   ```

1. **정책 - 정의** 블레이드에서 **새 이니셔티브 정의** 블레이드로 이동합니다.

1. **새 이니셔티브 정의** 블레이드에서 다음 설정을 사용하여 새 이니셔티브 정의를만듭니다.

    - 정의 위치: 이 랩에서 사용 중인 구독의 이름

    - 이름: **az10001b -태그 지정 이니셔티브**

    - 설명: **태그 정책의 컬렉션입니다.**

    - 범주: **랩**

    - 정책 및 매개 변수: **az10001b -감사 태그 및 해당 값**

        - 태그 이름: **환경**

        - 태그 값: **랩**

1. **정책 - 할당** 블레이드로 이동합니다.

1. **정책 - 할당** 블레이드에서 이니셔티브 블레이드 할당으로 이동하여 다음 설정을 사용하여 **새 기본할당**을 만듭니다:

    - 범위: 이 랩에서 사용 중인 구독의 이름

    - 제외: 없음 

    - 이니셔티브 정의: **az10001b -태그 지정 이니셔티브**

    - 과제 이름: **az10001b -태그 지정 이니셔티브** 할당

    - 설명: **az10001b 할당 - 태그 지정 이니셔티브**

    - 할당: 기본값

    - 관리되는 ID 만들기: **선택 취소**

1. **정책 - 규정 준수** 블레이드로 이동합니다. **규정 준수** 상태는 **등록되지 않았거나** **시작되지** 않음으로 설정되어 있습니다.

   > **참고**: 규정 준수 검사를 시작하는 데 평균적으로 약 10분이 걸립니다. 규정 준수 검사를 기다리는 대신 다음 작업으로 진행합니다. 이 연습의 후반부에서 규정 준수 상태를 검토합니다.


#### 작업 3: 리소스 태그 지정 준수를 적용하는 정책을 구현합니다.

1. **정책 - 정의** 블레이드로 이동합니다.

1. **정책 - 정의** 블레이드에서 **az10001b로 이동 - 태깅 이니셔티브** 블레이드.

1. **az10001b - 태깅 이니셔티브** 블레이드에서 **편집 이니셔티브** 정의 블레이드로이동합니다.

1. **Enforce 태그 및 해당 값** 이라고 이름지은 라는 기본 제공 정책 정의를 이니셔티브에 추가하고 매개변수를 다음 값으로 설정합니다.

    - 태그 이름: **환경**

    - 태그 값: **랩**

   > **참고**: 이 시점에서 이니셔티브에는 두 가지 정책이 포함되어 있습니다. 첫 번째는 규정 준수 상태를 평가하고 두 번째는 배포 중에 태그 지정을 적용합니다. 


#### 작업 4: 태그 지정 적용 및 태그 지정 준수를 평가합니다.

1. Azure 포털에서 **새** 블레이드로이동합니다.

1. **새** 블레이드에서 Azure 마켓플레이스에서 **템플릿 배포** 를 검색합니다.

1. 검색 결과 목록을 사용하여 **사용자 지정** 배포 블레이드로 이동합니다.

1. **사용자 지정 배포** 블레이드에서 **편집기에서 사용자 고유의 템플릿 빌드** 를 선택합니다.

1. **편집 템플릿** 블레이드에서 템플릿 파일 **az-100-01b_azuredeploy.json** 을 로드합니다. 

   > **참고**: 이 템플릿은 이 연습의 첫 번째 작업에서 배포에 사용한 템플릿과 동일합니다. 

1. 템플릿을 저장하고 **사용자 지정 배포** 블레이드로 돌아갑니다. 

1. **사용자 지정 배포 블레이드** 에서 **매개 변수 편집** 블레이드로 이동합니다.

1. **편집 매개 변수** 블레이드에서 매개 변수 파일 **az-100-01b_azuredeploy.parameters.json** 을 로드합니다. 

1. 매개 변수를 저장하고 **사용자 지정 배포 ** 블레이드로 돌아갑니다. 

1. **사용자 지정 배포** 블레이드에서 다음 설정을 사용하고 템플릿 배포를 시작합니다:

    - 구독: 이 랩에서 사용 중인 구독의 이름

    - 리소스 그룹: 새 리소스 그룹 **az1000102b-RG** 의 이름

    - 위치: 이 연습의 첫 번째 작업에서 선택한 Azure 지역의 이름입니다.

    - Vm 크기: **Standard_DS1_v2**

    - Vm 이름: **az1000102b-vm1**

    - 관리자 사용자 이름: **학생**

    - 관리자 암호: **Pa55w.rd1234**

    - 가상 네트워크 이름: **az1000102b-vnet1**

    - 환경 이름: **랩**

   > **참고**: 배포가 실패될 수 잇습니다. 예상되는 상황입니다.

1. 유효성 검사 에러를 나타내는 메시지가 표시됩니다. 리소스 **az1000102b-vnet1** 의 배포가 정책 적용 **태그 및 az10001b** - **태그 지정 이니셔티브 할당** 에 포함된 해당 값에 의해 허용되지 않음을 나타내는 오류 세부 정보를 검토합니다.

1. **정책 - 규정 준수** 블레이드로 이동합니다. **규정 준수 상태** 열의 항목을 식별합니다.

1. **az10001b로 이동 - 이니셔티브 할당** 블레이드 태그 지정및 규정 준수 상태 요약을 다시 활성화합니다.

1. 리소스 규정 준수 목록을 표시하고 비준수로 식별된 리소스를 확인합니다. 

   > **참고**: **규정 준수 상태** 에 대한 업데이트를 보려면 정책 - 규정 준수 블레이드에서 **새로 고침** 단추를 클릭해야 할 수 있습니다. 


#### 작업 5: 리소스 태그 지정 비준수에 대한 수정을 구현합니다.

1. Azure 포털에서 **az10001b - 태그 지정이니셔티브** 블레이드로 이동합니다.

1. **az10001b - 태깅 이니셔티브** 블레이드에서 **편집 이니셔티브** 정의 블레이드로이동합니다.

1. 기본 제공 정책 정의인 **Apply tag및 기본값** 을 이니셔티브에 추가하고매개 변수를 다음 값으로 설정합니다.

    - 태그 이름: **환경**

    - 태그 값: **랩**

1. **az10001b라는** 사용자 지정 정책 정의 - 감사 태그 및 이니셔티브의 값을 삭제합니다.

1. **Enforce 태그라는 기본 제공 정책 정의와 해당 값** 을 이니셔티브에서 삭제하고 변경 내용을저장합니다.

   > **참고**: 이 시점에서 이니셔티브에는 새 리소스 를 배포하는 동안 태그 지정 비준수를 자동으로 수정하고 규정 준수 상태를 평가하는 단일 정책이 포함되어 있습니다.

1. Azure 포털에서 클라우드 셸에서 PowerShell 세션을 시작합니다. 

   > **참고**: 현재 Azure 구독에서 클라우드 셸을 처음 시작하는 경우 클라우드 셸 파일을 유지하도록 Azure 파일 공유를 만들라는 메시지가 표시됩니다. 이 경우 기본값을 허용하면 자동으로 생성된 리소스 그룹에서 저장소 계정이 생성됩니다.

1. 클라우드 셸 창에서 다음 명령을 실행합니다.

   ```
   Get-AzResource -ResourceGroupName 'az1000101b-RG' | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag @{environment="lab"} -Force }
   ```

   > **참고**: 이러한 명령은 리소스 그룹 **az1000101b-RG** 의 각 **리소스에 값** 랩이 있는 **환경** 태그를 할당하여 이미 할당된 태그를 덮어쓰게 합니다.
   
   > **참고**: 명령이 성공적으로 완료될 때까지 기다립니다.   

1. Azure 포털에서 **태그** 블레이드로 이동합니다.

1. **태그** 블레이드에서 **환경** 태그를 값 **랩** 으로 설정하여 모든 리소스를 표시합니다. 리소스 그룹 **az1000101b-RG** 의 모든리소스가 나열되어 있는지 확인합니다.


#### 작업 6: 수정 작업이 규정 준수에 미치는 영향을 평가합니다.

1. Azure 포털에서 **새** 블레이드로이동합니다.

1. **새** 블레이드에서 Azure 마켓플레이스에서 **템플릿 배포** 를 검색합니다.

1. 검색 결과 목록을 사용하여 **사용자 지정** 배포 블레이드로 이동합니다.

1. **사용자 지정 배포** 블레이드에서 **편집기에서 사용자 고유의 템플릿 빌드** 를 선택합니다.

1. **편집 템플릿** 블레이드에서 템플릿 파일 **az-100-01b_azuredeploy.json** 을 로드합니다. 

   > **참고**: 이 템플릿은 이 연습의 첫 번째 작업에서 배포에 사용한 템플릿과 동일합니다. 

1. 템플릿을 저장하고 **사용자 지정 배포** 블레이드로 돌아갑니다. 

1. **사용자 지정 배포** 블레이드에서 **매개 변수 편집** 블레이드로 이동합니다.

1. **편집 매개 변수** 블레이드에서 매개 변수 파일 **az-100-01b_azuredeploy.parameters.json** 을 로드합니다. 

1. 매개 변수를 저장하고 **사용자 지정 배포** 블레이드로 돌아갑니다. 

1. **사용자 지정 배포** 블레이드에서 다음 설정을 사용하고 템플릿 배포를 시작합니다:

    - 구독: 이 랩에서 사용 중인 구독의 이름

    - 리소스 그룹: **az1000102b-RG**

    - 위치: 이 연습의 첫 번째 작업에서 선택한 Azure 지역의 이름입니다.

    - Vm 크기: **Standard_DS1_v2**

    - Vm 이름: **az1000102b-vm1**

    - 관리자 사용자 이름: **학생**

    - 관리자 암호: **Pa55w.rd1234**

    - 가상 네트워크 이름: **az1000102b-vnet1**

    - 환경 이름: **랩**

   > **참고**: 이번에는 배포가 성공합니다. 예상되는 상황입니다.

   > **참고**: 다음 단계로 진행하기 전에 배포가 완료될 때까지 기다리지 마십시오. 

1. Azure 포털에서 **태그** 블레이드로 이동합니다.

1. **태그** 블레이드에서 **환경** 태그를 값 **랩** 으로 설정하여 모든 리소스를 표시합니다. 리소스 그룹 **az1000102b-RG에** 배포된 모든 리소스에는 동일한 값이 자동으로 할당된 이 태그가 있습니다.

   > **참고**: 이 시점에서 일부 리소스만 프로비전되었지만 모든 리소스에 할당된 태그가 있는지 확인해야 합니다.

1. **정책 - 규정 준수** 블레이드로 이동합니다. **규정 준수 상태** 열의 항목을 식별합니다.

1. **az10001b - 태그 지정 이니셔티브**할당 블레이드로 이동합니다. **규정 준수 상태** 열의 항목을 식별합니다. 열에 시작 되지 않음 항목이 포함된 경우 **규정 준수 검색** 이 실행될 때까지 기다립니다. 

   > **참고**: 규정 준수 상태에 대한 업데이트를 보려면 **정책 - 규정 준수** 블레이드에서 최대 10분 까지 기다렸다가 **새로 고침** 단추를 클릭해야 할 수 있습니다. 

   > **참고**: 상태가 준수로 나열될 때까지 기다리지 말고 다음 연습으로 진행합니다. 

> **결과**: 이 연습을 완료한 후에는 리소스 태그 지정 규정 준수를 평가, 적용 및 수정하는 이니셔티브 및 정책을 구현했습니다. 또한 정책 할당의 효과를 평가했습니다. 


### 연습 2: Azure 리소스 잠금 구현
  
이 연습의 주요 작업은 다음과 같습니다:

1. 실수로 변경되지 않도록 리소스 그룹 수준 잠금 만들기

1. 리소스 그룹 수준 잠금의 기능 유효성 검사


#### 작업 1: 실수로 변경되지 않도록 리소스 그룹 수준 잠금 만들기

1. Azure 포털에서 **az1000101b-RG** 리소스그룹 블레이드로 이동합니다.

1. **az1000101b-RG** 자원 그룹 블레이드에서 **az1000101b-RG - 잠금** 블레이드를 표시합니다.

1. **az1000101b-RG에서 -블레이드** 를 잠그고 다음 설정으로 잠금을 추가합니다:

    - 잠금 이름: **az1000101b-roLock**

    - 잠금 유형: **읽기 전용**


#### 작업 2: 리소스 그룹 수준 잠금의 기능 유효성 검사

1. Azure 포털에서 **az1000102b-vm1** 가상컴퓨터 블레이드로 이동합니다.

1. **az1000102b-vm1 가상** 머신 블레이드에서 **az1000102b-vm1 - 태그** 블레이드로 이동합니다.

1. **환경** 태그의 값을 **개발자로** 설정해 보십시오. 파일 첨부 확인 

1. Azure 포털에서 **az1000101b-vm1** 가상컴퓨터 블레이드로 이동합니다.

1. **az1000101b-vm1 가상** 머신 블레이드에서 **az1000101b-vm1 - 태그** 블레이드로 이동합니다.

1. **환경** 태그의 값을 **개발자로** 설정해 보십시오. 이번에는 작업이 실패합니다. 결과 오류 메시지는 리소스가 태그 할당을 거부했음을 나타내며 리소스 잠금이 원인일 수 있습니다.

1. **az1000101b-RG - Locks** 리소스 그룹에 생성된 저장소 계정의 블레이드로 이동합니다. 

1. 저장소 계정 블레이드에서 **액세스 키** 블레이드로 이동합니다. 리소스 또는 해당 부모에 대한 읽기 잠금 때문에 데이터 평면에 액세스할 수 없다는 오류 메시지가 표시됩니다.

1. Azure 포털에서 **az1000101b-RG** 리소스그룹 블레이드로 이동합니다.

1. **az1000101b-RG** 리소스 그룹 블레이드에서 태그 블레이드로 이동합니다. ****

1. **태그** 블레이드에서 값 **랩** 을 사용하여 환경 태그를 리소스 그룹에 **할당하고** 오류 메시지를 기록합니다.


> **결과**: 이 연습을 완료한 후 실수로 변경되지 않도록 리소스 그룹 수준 잠금을 만들고 해당 기능의 유효성을 검사했습니다. 
