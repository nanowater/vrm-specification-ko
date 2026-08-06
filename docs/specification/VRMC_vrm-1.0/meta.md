# `VRMC_vrm.meta`

본 문서에서는 `VRMC_vrm` 확장 중 `meta` 필드에 대한 사양을 설명합니다.

## Meta

VRM의 `meta` 필드에서는 모델에 관한 메타 정보를 작성할 수 있습니다.
`meta`에는 모델의 이름, 제작자와 같은 기본 모델 정보뿐만 아니라 모델의 이용 조건에 관한 정보도 포함됩니다.

## 라이선스

VRM 확장에서는 모델의 라이선스 정보를 `meta` 필드에 기술할 수 있습니다.

`meta` 필드에 저장되는 라이선스 정보는 라이선스 문서의 URL과 라이선스 설정으로 구성됩니다.
라이선스 문서는 VRM 컨소시엄에 의해 제정된 VRM 퍼블릭 라이선스 문서를 가리키며, `meta.licenseUrl`에 해당 문서의 고유한 URL이 저장되어 있어야 합니다.
라이선스 설정은 라이선스 문서에서 참조하며, 모델의 라이선스 부여자가 직접 지정할 수 있는 설정 항목입니다.

### licenseUrl

VRM1.0에서 `licenseUrl`속성은 `https://vrm.dev/licenses/1.0/`을 허용합니다.

## glTF Schema Updates

### 프로퍼티

| 이름                           | 값         | 설명                                                                         | 필수                            |
| :----------------------------- | :--------- | :--------------------------------------------------------------------------- | :------------------------------ |
| name                           | `string`   | 모델의 이름                                                                  | ✅ Yes                          |
| version                        | `string`   | 모델의 버전                                                                  | No                              |
| authors                        | `string[]` | 모델의 제작자 이름                                                           | ✅ Yes                          |
| copyrightInformation           | `string`   | 모델의 저작권자                                                              | No                              |
| contactInformation             | `string`   | 모델의 제작자 연락처                                                         | No                              |
| references                     | `string[]` | 모델의 원작이 있다면 그 정보                                                 | No                              |
| thirdPartyLicenses             | `string`   | 모델의 서드파티 라이선스 표기                                                | No                              |
| thumbnailImage                 | `integer`  | 모델의 썸네일로 사용될 이미지의 인덱스                                       | No                              |
| licenseUrl                     | `string`   | 이 모델이 참조하는 VRM 라이선스 문서의 URL                                   | ✅ Yes                          |
| avatarPermission               | `string`   | 이 모델을 아바타로 사용할 수 있는 주체/권한                                  | No, 초기값: `OnlyAuthor`        |
| allowExcessivelyViolentUsage   | `boolean`  | 이 모델을 과도한 폭력 표현이 포함된 콘텐츠에서 사용하는 것을 허락할지 여부   | No, 초기값: `false`             |
| allowExcessivelySexualUsage    | `boolean`  | 이 모델을 과도한 성적 표현이 포함된 콘텐츠에서 사용하는 것을 허락할지 여부   | No, 초기값: `false`             |
| commercialUsage                | `string`   | 이 모델을 이용한 상업적 이용의 허락 범위                                     | No, 초기값: `personalNonProfit` |
| allowPoliticalOrReligiousUsage | `boolean`  | 이 모델을 정치·종교적 목적으로 사용하는 것을 허락할지 여부                   | No, 초기값: `false`             |
| allowAntisocialOrHateUsage     | `boolean`  | 이 모델을 반사회적·혐오 표현이 포함된 콘텐츠에서 사용하는 것을 허락할지 여부 | No, 초기값: `false`             |
| creditNotation                 | `string`   | 이 모델의 크레딧 표기를 강제하거나 생략하도록 하는 옵션                      | No, 초기값: `required`          |
| allowRedistribution            | `boolean`  | 이 모델의 재배포를 허락할지 여부                                             | No, 초기값: `false`             |
| modification                   | `string`   | 이 모델의 수정/개조 허락 범위                                                | No, 초기값: `prohibited`        |
| otherLicenseUrl                | `string`   | 기타 라이선스 조건이 있다면 그 URL                                           | No                              |

### meta.name ✅

모델의 이름입니다.

- 타입: `string`
- 필수 여부: 필수

### meta.version

모델의 버전입니다.

- 타입: `string`
- 필수 여부: 선택

### meta.authors ✅

모델의 제작자 이름입니다.

반드시 하나 이상의 빈 문자열이 아닌 항목이 필요합니다.
첫 번째 항목에는 모델 창작자/대표 제작자의 이름을 기재하는 것을 권장합니다.

- 타입: `string[]`
- 필수 여부: 필수

### meta.copyrightInformation

모델의 저작권자입니다.

이 모델의 저작권을 표시하기 위해 사용되는 것을 상정하고 있으며, 직전의 `authors`와는 명확히 구분하여 다룹니다.

- 타입: `string`
- 필수 여부: 선택

### meta.contactInformation

모델의 제작자(대표자)의 연락처입니다.

> 사용자가 어떤 이유로 제작자에게 연락을 취하고 싶을 때를 대비하여 소셜 계정 정보나 웹사이트 등의 연락처를 표시하기 위해 사용됩니다.
> 전화번호나 주소 등 대중에게 공개되는 것을 의도하지 않는 개인 정보는 포함하지 않도록 주의하십시오.

- 타입: `string`
- 필수 여부: 선택

### meta.references

모델의 참조 자료 / 원작 목록입니다.

- 타입: `string[]`
- 필수 여부: 선택

### meta.thirdPartyLicenses

모델의 서드파티 라이선스 표기가 필요하다면 여기에 기술합니다.

줄바꿈을 사용하여 여러 줄에 걸친 문서를 기술할 수 있습니다.

- 타입: `string`
- 필수 여부: 선택

### meta.thumbnailImage

`gltf.images`내에서 모델의 썸네일 이미지에 접근하기 위한 인덱스입니다.

이미지는 정사각형이어야 합니다. 해상도는 1024x1024를 권장합니다.
편의성을 위해 이미지는 glTF 코어 사양에서 지원하는 JPG 또는 PNG 형태여야 합니다.
확장 기능을 통한 다른 포맷 사용은 허용되지 않습니다.

VRM을 사용하는 애플리케이션에서 모델의 아이콘으로 활용하도록 설계되었습니다.

- 타입: `integer`
- 필수 여부: 선택
- 최솟값: `>= 0`

### meta.licenseUrl ✅

이 모델이 참조하는 라이선스 문서의 URL을 지정합니다.
VRM 퍼블릭 라이선스 문서의 고유한 URL이 저장되어 있어야 합니다.

- 타입: `string`
- 필수 여부: 필수

### meta.avatarPermission

이 모델을 아바타로 사용할 수 있는 권한을 가진 주체입니다.

`onlyAuthor`는 모델의 제작자에게만 아바타로서의 조작이 허락됨을 의미합니다.
`onlySeparatelyLicensedPerson`은 별도의 문서로 라이선스 허락을 받은 사용자에게 아바타로서의 조작이 허락됨을 의미합니다. 예를 들어, 유료로 판매되는 모델에 대해 사용되는 것을 상정하고 있습니다.
`everyone`은 누구에게나 이 모델을 아바타로서 조작하는 것이 허락됨을 의미합니다.

- 타입: `string`
- 필수 여부: 선택, 초기값: `onlyAuthor`
- 허용되는 값:
  - `onlyAuthor`
  - `onlySeparatelyLicensedPerson`
  - `everyone`

### meta.allowExcessivelyViolentUsage

이 모델을 과도한 폭력 표현이 포함된 콘텐츠에 이용하는 것을 허락할지 여부를 지정합니다.

- 타입: `boolean`
- 필수 여부: 선택, 초기값: `false`

### meta.allowExcessivelySexualUsage

이 모델을 과도한 성적 표현이 포함된 콘텐츠에 이용하는 것을 허락할지 여부를 지정합니다.

- 타입: `boolean`
- 필수 여부: 선택, 초기값: `false`

### meta.commercialUsage

이 모델을 상업적으로 이용하는 것을 허락할지 지정합니다.

`personalNonProfit`은 개인 사용자가 비상업적 목적에 한하여 이용하는 것을 허락함을 의미합니다.
`personalProfit`은 개인 사용자가 상업적·비상업적 목적과 관계없이 이용하는 것을 허락함을 의미합니다.
법인 사용자는 이 프로퍼티가 `corporation`이 아닌 한 해당 모델의 이용이 허락되지 않습니다.

- 타입: `string`
- 필수 여부: 선택, 초기값: `personalNonProfit`
- 허용되는 값:
  - `personalNonProfit`
  - `personalProfit`
  - `corporation`

### meta.allowPoliticalOrReligiousUsage

이 모델을 정치적·종교적인 콘텐츠에 대해 이용하는 것을 허락할지 여부를 지정합니다.

- 타입: `boolean`
- 필수 여부: 선택, 초기값: `false`

### meta.allowAntisocialOrHateUsage

이 모델을 반사회적·혐오 표현이 포함된 콘텐츠에 대해 이용하는 것을 허락할지 여부를 지정합니다.

- 타입: `boolean`
- 필수 여부: 선택, 초기값: `false`

### meta.creditNotation

이 모델의 크레딧 표기를 요구할지 지정합니다.

이 프로퍼티가 `required`인 경우, 사용자는 반드시 모델의 크레딧 표기를 해야 합니다.
이 프로퍼티가 `unnecessary`인 경우, 사용자는 모델의 크레딧 표기를 반드시 할 필요는 없습니다.

- 타입: `string`
- 필수 여부: 선택, 초기값: `required`
- 허용되는 값:
  - `required`
  - `unnecessary`

### meta.allowRedistribution

이 모델을 재배포하는 것을 허락할지 여부를 지정합니다.

- 타입: `boolean`
- 필수 여부: 선택, 초기값: `false`

### meta.modification

이 모델을 개조(변경)하는 것을 허락할지 여부 및 개조한 모델의 재배포를 허락할지 여부를 지정합니다.

`prohibited`인 경우 모델의 개조를 허락하지 않음을 의미합니다.<br>
`allowModification` 또는 `allowModificationRedistribution`인 경우 모델의 개조를 허락함을 의미합니다.<br>
`allowModificationRedistribution`인 경우 개조한 모델의 재배포를 허락함을 의미합니다.

- 타입: `string`
- 필수 여부: 선택, 초기값: `prohibited`
- 허용되는 값:
  - `prohibited`
  - `allowModification`
  - `allowModificationRedistribution`

### meta.otherLicenseUrl

기타 라이선스 조건이 있다면 그 URL을 지정합니다.

- 타입: `string`
- 필수 여부: 선택
