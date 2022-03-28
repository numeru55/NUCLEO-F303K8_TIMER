# 概要
タイマー割り込み
# ioc
- クロック周波数確認 8MHz
- データシートで確認。Basic TimerであるTIM6を使うことにする。
- iocでTIM6を有効化。
- TIM6のNVICを有効化。
- PrescalerとAutoReloadで周期設定 それぞれ8000と1000にすることで，
```
8MHz/8000/1000=1Hz
```
となる。

# main.c

割り込みが発生したときの処理を記述

```main.c
/* USER CODE BEGIN 4 */
void  HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim){
    HAL_GPIO_TogglePin(LED_GPIO_Port,LED_Pin);
}
/* USER CODE END 4 */
```

タイマー開始等。周期変更もやってみた。スマートじゃないかもしれないがレジスター直書きしてみた。NUCLEOの左にある，4-5のショートピンを挿抜することで点滅周期が変わる。

```main.c
/* USER CODE BEGIN 2 */
HAL_TIM_Base_Start_IT(&htim6); // タイマーを割り込み有りで開始
/* USER CODE END 2 */

/* Infinite loop */
/* USER CODE BEGIN WHILE */
while (1)
{
  /* USER CODE END WHILE */

  /* USER CODE BEGIN 3 */
  if (HAL_GPIO_ReadPin(D2_PA12_GPIO_Port, D2_PA12_Pin)) {
      TIM6->ARR = 1000-1; // 1000/1000=1sec when D2 pin is OFF
    } else {
      TIM6->ARR = 100-1;  // 1000/100=0.1sec when D2 pin is ON
  }
}
/* USER CODE END 3 */
```
