# Arrhythmia Detection

## Concepts
Arrhythmia Detection은 심전도(ECG) 파형 데이터를 분석하여, 해당 파형이 부정맥인지를 검출해주는 라이브러리입니다.

### 검출 가능한 부정맥 종류
현재 검출이 가능한 부정맥의 종류는 다음과 같습니다.
- 심근 허혈증 (Ischemia)
<!--
- 조기심실수축 (PVC, Premature ventricular Contraction)
-->
- 이단맥 (Bigeminy)
- 삼단맥 (Trigeminy)
- 심정지 (Asystole)
<!--
- 심방 세동 (AF, Atrial Fibrillation)
- 빈맥 (Tachycardia)
- 심실빈맥 (VT, Ventricular Tachycardia)
- 상심실성 빈맥 (SVT, Supraventricular Tachycardia)
- 서맥 (Bradycardia)
-->

이후 지속적으로 다양한 부정맥 종류들을 추가할 예정입니다.
- 조기심실수축 (PVC, Premature ventricular Contraction)
- 심방 세동 (AF, Atrial Fibrillation)
- 빈맥 (Tachycardia)
- 심실빈맥 (VT, Ventricular Tachycardia)
- 상심실성 빈맥 (SVT, Supraventricular Tachycardia)
- 서맥 (Bradycardia)

## 제공 API 기본 구조

### 부정맥 (Arrhythmia) 정의
본 라이브러리에서 부정맥 종류는 아래와 같은 열거형으로 정의됩니다.
```C++
enum class Arrhythmia : unsigned
{
    Normal = 0x0,       // Not arrhythmia
    Ischemia = 0x1,     // Ischemia
    PVC = 0x2,          // Premature ventricular Contraction
    Bigeminy = 0x4,     // Bigeminy
    Trigeminy = 0x8,    // Trigeminy
    Asystole = 0x10,    // Asystole
    AF = 0x20,          // Atrial Fibrillation
    Tachycardia = 0x40, // Tachycardia
    VT = 0x80,          // Ventricular Tachycardia
    SVT = 0x100,        // Supraventricular Tachycardia
    Bradycardia = 0x200,// Bradycardia
};
```
추가로, ECG 파형에 따라 다수의 부정맥이 검출될 수 있으므로, bitmask OR 처리를 통해 중복 처리가 가능합니다.

### 검출 함수 정의
부정맥 검출을 위해 호출 가능한 함수들은 아래와 같이 정의됩니다.

<!--
1. 기간별 검출
```C++
/**
 * @brief 단기간 데이터 (60초 이내) 내에서 부정맥을 검출합니다.
 *
 * @param samples 심전도(ECG) 파형 샘플 데이터 목록 (Sample rate는 250으로 고정해야 함)
 * @param sampleCount 심전도 파형 샘플 데이터 개수
 * @return
 *     Arrhythmia::Normal (정상)
 *     Arrhythmia::Ischemia (심근 허혈)
 *     Arrhythmia::PVC (조기심실수축)
 *     Arrhythmia::Bigeminy (이단맥)
 *     Arrhythmia::Trigeminy (삼단맥)
 *     Arrhythmia::Asystole (심정지)
 **/
Arrhythmia detect_Short_Term_Signal(std::vector<float> samples, int sampleCount);

/**
 * @brief 장기간 데이터 (60초 이상) 내에서 부정맥을 검출합니다.
 *
 * @param samples 심전도(ECG) 파형 샘플 데이터 목록 (Sample rate는 250으로 고정해야 함)
 * @param sampleCount 심전도 파형 샘플 데이터 개수
 * @return
 *     Arrhythmia::Normal (정상)
 *     Arrhythmia::AF (심근 허혈)
 *     Arrhythmia::Tachycardia (빈맥)
 *     Arrhythmia::VT (심실빈맥)
 *     Arrhythmia::SVT (상심실성 빈맥)
 *     Arrhythmia::Bradycardia (서맥)
 **/
Arrhythmia detect_Long_Term_Signal(std::vector<float> samples, int sampleCount);
```
-->

1. 종류별 검출
```C++
/**
 * @brief 심근 허혈을 검출합니다.
 *
 * @param samples 심전도(ECG) 파형 샘플 데이터 목록 (Sample rate는 250으로 고정해야 함)
 * @param sampleCount 심전도 파형 샘플 데이터 개수
 * @return
 *     Arrhythmia::Normal (정상)
 *     Arrhythmia::Ischemia (심근 허혈)
 **/
Arrhythmia detect_Ischemia(std::vector<float> samples, int sampleCount);

/**
 * @brief 이단맥, 삼단맥, 심정지를 검출합니다.
 *
 * @param samples 심전도(ECG) 파형 샘플 데이터 목록 (Sample rate는 250으로 고정해야 함)
 * @param sampleCount 심전도 파형 샘플 데이터 개수
 * @return
 *     Arrhythmia::Normal (정상)
 *     Arrhythmia::Bigeminy (이단맥)
 *     Arrhythmia::Trigeminy (삼단맥)
 *     Arrhythmia::Asystole (심정지)
 **/
Arrhythmia detect_Bigeminy_Trigeminy_Asystole(std::vector<double> samples, int sampleCount);
```

<!--
/**
 * @brief 심방 세동을 검출합니다.
 *
 * @param samples 심전도(ECG) 파형 샘플 데이터 목록 (Sample rate는 250으로 고정해야 함)
 * @param sampleCount 심전도 파형 샘플 데이터 개수
 * @return
 *     Arrhythmia::Normal (정상)
 *     Arrhythmia::AF (심방 세동)
 **/
Arrhythmia detect_Atrial_Fibrillation(std::vector<double> samples, int sampleCount);

/**
 * @brief 심실빈맥을 검출합니다.
 *
 * @param samples 심전도(ECG) 파형 샘플 데이터 목록 (Sample rate는 250으로 고정해야 함)
 * @param sampleCount 심전도 파형 샘플 데이터 개수
 * @return
 *     Arrhythmia::Normal (정상)
 *     Arrhythmia::PVC (조기심실수축)
 *     Arrhythmia::Tachycardia (빈맥)
 *     Arrhythmia::VT (심실빈맥)
 *     Arrhythmia::SVT (상심실성 빈맥)
 *     Arrhythmia::Bradycardia (서맥)
 **/
Arrhythmia detect_VT_SVT(std::vector<float> samples, int sampleCount);
-->

## Copyright
Copyright (c) 2020-2021 Ncube Inc.

All rights reserved.
