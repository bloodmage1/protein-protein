# 비언어적 음성 데이터를 이용할 수 있을까?

# 목차

1. 단백질 시퀀스가 서로 붙는 경우의 수
   - 직렬 결합
   - 역직렬 결합

2. 단백질 간 수소결합
   - 각 단백질 끝 자리가 산과 염기일 경우 결합
   - 각 단백질 끝 자리가 소수성일 경우 소수성끼리만 결합
   - 각 단백질 끝 자리가 극성일 경우 극성끼리만 결합

---

## 단백질 시퀀스가 서로 붙는 경우의 수

1. 직렬 결합 (N-터미널에서 C-터미널로)

- 시퀀스 A: ABCDE
- 시퀀스 B: FGHIJ
- 결합 시퀀스: ABCDEFGHIJ

2. 역 직렬 결합 (N-터미널에서 C-터미널로)

- 시퀀스 A: ABCDE
- 시퀀스 B: FGHIJ
- 결합 시퀀스: JIHGFEDCBA

```
df["frag_3"] = 0
df["frag_4"] = 0

for i in tqdm(range(len(df))):
    df["frag_3"][i] = df["frag_1"][i][::-1]
    df["frag_4"][i] = df["frag_2"][i][::-1]
```

## 단백질 간 수소결합

여러 단백질이 상호작용하여 단백질 복합체를 형성할 때, 단백질 간 수소 결합이 형성될 수 있습니다.

- 각 단백질 끝 자리가 산과 염기일 경우 결합
- 각 단백질 끝 자리가 소수성일 경우 소수성끼리만 결합
- 각 단백질 끝 자리가 극성일 경우 극성끼리만 결합

### 소수성 아미노산:
글리시 (Gly)
알라닌 (Ala)
발린 (Val)
로이신 (Leu)
이소류신 (Ile)
메티오닌 (Met)
프롤린 (Pro)

### 극성 아미노산:
세린 (Ser)
트레오닌 (Thr)
시스테인 (Cys)
아스파라진 (Asn)
글루타민 (Gln)

### 산성 아미노산:
아스파르트산 (Asp)
글루타민산 (Glu)

### 염기성 아미노산:
아르지닌 (Arg)
리신 (Lys)
히스티딘 (His)


```
df["check_cha"] = 0
df["check_num"] = 0

sosu = ["V", "L", "I", "A", "F", "Y", "M", "W", "G", "P"] # 소수성
kuk = ["T", "S", "N","Q","C"] # 극성
san = ["E","D"] # 산성
yum = ["H","R","K"] # 염기성

for i in tqdm(range(len(df))):

    """
    frag_1 의 첫글자 - frag_2 첫글자
    """        
        
    if df["frag_1"][i][0] in san and df["frag_2"][i][0] in yum:
        df["check_cha"][i] = "1-sanyum"
    if df["frag_1"][i][0] in yum and df["frag_2"][i][0] in san:
        df["check_cha"][i] = "1-sanyum"    
    
    """
    frag_1 첫글자 - frag_2 마지막글자
    """

    if df["frag_1"][i][0] in san and df["frag_2"][i][-1] in yum:
        df["check_cha"][i] = "2-sanyum"
    if df["frag_1"][i][0] in yum and df["frag_2"][i][-1] in san:
        df["check_cha"][i] = "2-sanyum"
        
    """
    frag_1 마지막글자 - frag_2 첫글자
    """

    if df["frag_1"][i][-1] in san and df["frag_2"][i][0] in yum:
        df["check_cha"][i] = "3-sanyum"
    if df["frag_1"][i][-1] in yum and df["frag_2"][i][0] in san:
        df["check_cha"][i] = "3-sanyum"            
        
        
    """
    frag_1 마지막글자 - frag_2 마지막글자
    """

    if df["frag_1"][i][-1] in san and df["frag_2"][i][-1] in yum:
        df["check_cha"][i] = "4-sanyum"
    if df["frag_1"][i][-1] in yum and df["frag_2"][i][-1] in san:
        df["check_cha"][i] = "4-sanyum"    

df["comb"] = 0

"""
df["comb1"] = df["frag_1"] + df["frag_2"] 첨글자 + 첨글자
df["comb2"] = df["frag_1"] + df["frag_4"] 첨글자 + 마지막 글자
df["comb3"] = df["frag_2"] + df["frag_1"] 마지막 글자 + 첨글자
df["comb4"] = df["frag_2"] + df["frag_3"] 마지막 글자 + 마지막 글자
"""
#산성염기성 결합이면 combination 값을 ['comb']에 넣음 (두 단백질 붙인 sequence)
for i in tqdm(range(len(df))):
    if df["check_cha"][i] == "4-sanyum":
        df["comb"][i] = df["frag_2"][i] + df["frag_3"][i]
    if df["check_cha"][i] == "3-sanyum":
        df["comb"][i] = df["frag_2"][i] + df["frag_1"][i]
    if df["check_cha"][i] == "2-sanyum":
        df["comb"][i] = df["frag_1"][i] + df["frag_4"][i]
    if df["check_cha"][i] == "1-sanyum":
        df["comb"][i] = df["frag_1"][i] + df["frag_2"][i]

```