# GIT FETCH & PULL

> 팀으로 프로젝트를 진행하며 작업한 것을 병합하고 브랜치를 관리하면서
> Git 명령어에 대해 더 자세하게 알아야겠다라고 생각했고
> 가장 많이 사용하고있는 두 명령어에 대해서 정리를 해야겠다라고 생각했다.

### Clone 저장소

> - 원격 저장소를 복제한 저장소를 클론 저장소라고 부른다
> - 원격 저장소에 변경 사항이 생겼다면, 그 내용을 가져와 클론 저장소를 최신 상태로 유지해주어한다
> - 이때 fetch 와 pull 이 등장한다.

### FETCH

Fetch는 로컬 Git에게 원격 저장소에서 최신 메타데이터 정보를 확인하라는 명령을 전달한다.
하지만 변경 사항이 있는지 확인만 하고, 변경된 데이터를 로컬 Git에 실제로 가져오지는 않는다.

### PULL

Pull은 원격 저장소에서 변경된 메타데이터 정보를 확인할 뿐만 아니라 최신 데이터를 복사하여 로컬 Git에 가져온다.