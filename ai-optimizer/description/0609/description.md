## 문제 상황

현재 'agent_aiOpt_cep' 기반으로 답변을 받아봤는데, 실제 수치와 값이 다르거든?

- 입력된 데이터: 94cb5f31-64e4-48fc-b0fc-473704e2fca3.md
- 답변 결과: v.1.0.0_answer.md

- 이 이미지를 보면 화면의 값과 프롬프트 기반 응답 값의 수치가 다르죠?(image-1.png)
- 보니까 실제 사용된 데이터는 아래와 같은데, 실제 화면에서 보이는 값이 차이가 있어요.

```
CEP8,In a casual or client conversation  you want to quickly show information (a website  photo set  or document) so others can see it clearly.,36230,87.2,98,0,48.9,양호,최우선 기회,1
```

- 이를 프롬프트단에서 연산처리 해야 할 것 같은데 가능할까?
