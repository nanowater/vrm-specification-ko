# `VRMC_vrm.firstPerson`

!!! warning "🤖 AI 자동 번역 문서"

    이 문서는 AI로 자동 번역된 문서입니다. 아직 검수가 완료되지 않았으므로 **오역이나 부정확한 표현이 포함될 수 있습니다**.

    정확한 내용은 [원본 vrm-specification 문서](https://github.com/vrm-c/vrm-specification)와 비교하여 확인해 주세요.

본 문서에서는 `VRMC_vrm` 확장 중 `firstPerson` 필드에 대한 사양을 설명합니다.

## MeshAnnotation

Mesh 단위의 렌더링을 제어합니다.

`extensions.VRMC_vrm.firstPerson.meshAnnotations[*]`

| 이름            | 비고                           |
| :-------------- | :----------------------------- |
| node            | 대상 node(mesh를 가짐)의 index |
| firstPersonFlag | 후술                           |

firstPersonFlag. VR 앱에서 모델을 사용할 경우, 자신의 모델 렌더링을 HMD와 그 외의 카메라로 구분합니다.

## MeshAnnotation(enum)

`thirdPersonOnly`는 VR 렌더링 시 자신의 머리 부분을 숨기는 것을 의도합니다. VR 시점 카메라는 머리 안에 위치하는 것으로 가정됩니다. 따라서 얼굴, 머리, 머리카락의 폴리곤이 시야에 보이는 것을 방지합니다.

특별히 렌더링을 구분할 필요가 없는 오브젝트는 `both`로 설정하여, 모든 카메라에서 렌더링되도록 합니다.

`firstPersonOnly`는 자신에게만 보이고 타인의 시점에서는 보이지 않는 설정입니다. 앱 측에서 특별한 용도가 없다면 사용하지 않습니다.

`auto`는 Mesh를 머리(`thirdPersonOnly`)와 그 외(`both`)로 분할해 줍니다. 다음 절에서 자세히 설명합니다.

| 이름            | 1인칭 카메라(VR 시점) | 그 외의 카메라 | 예시                                                                |
| :-------------- | :-------------------- | :------------- | ------------------------------------------------------------------- |
| thirdPersonOnly | 렌더링 안 함          | 렌더링 함      | 얼굴, 눈, 머리, 머리카락, 모자, 헬멧 등 VR 시야를 가리는 것         |
| firstPersonOnly | 렌더링 함             | 렌더링 안 함   | 플레이어에게만 보이고 타인의 시점에서는 보이지 않는 유저 인터페이스 |
| both            | 렌더링 함             | 렌더링 함      |                                                                     |
| auto            |                       |                | 후술                                                                |

### MeshAnnotation.Auto의 알고리즘

- Mesh의 모든 정점을 검사하여, Head 본과 그 자손 본에 대한 Weight를 가진 정점을 수집합니다.
- 위의 정점을 포함하는 삼각형과 포함하지 않는 삼각형으로 이등분한 Mesh를 생성합니다.
- 위의 정점을 포함하는 Mesh를 ThirdPersonOnly, 포함하지 않는 Mesh를 Both로 설정합니다.

### MeshAnnotation이 지정되지 않은 경우

MeshAnnotation이 지정되지 않은 메쉬가 존재하는 경우 FirstPerson 기능을 이용할 때,
해당 메쉬에는 `auto`가 설정된 것으로 간주하여 처리하십시오.

`firstPerson` 속성 자체가 VRM 확장에 존재하지 않는 경우에도,
모든 메쉬에 `auto`가 설정된 것으로 간주하여 처리하십시오.
