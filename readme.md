# Projekt poziomica

## Konfiguracja ACC
### Aby skorzystać z ACC w konfiguracji I2C należy pobrać bibliotekę:
 
### 1. Kliknij na "Płytka" > "Zarządzaj bibliotekami" w górnym menu.
### 2. W polu wyszukiwania wpisz "I2C" i znajdź bibliotekę "STM32F7xx_HAL_Driver".
### 3. Zaznacz tę bibliotekę i kliknij "Dodaj do projektu".
### Następnie dodać kod:
#include "stm32f7xx_hal.h" //biblioteka HAL
#define LIS3DSH_ADDRESS 0x1E // Adres akcelometru
### A następnie, aby utworzyć funkcje HAL_I2C_MspInit(): 
```ruby
static void MX_I2C1_Init(void)
{
  hi2c1.Instance = I2C1;
  hi2c1.Init.Timing = 0x00707CBB;
  hi2c1.Init.OwnAddress1 = 0;
  hi2c1.Init.AddressingMode = I2C_ADDRESSINGMODE_7BIT;
  hi2c1.Init.DualAddressMode = I2C_DUALADDRESS_DISABLE;
  hi2c1.Init.OwnAddress2 = 0;
  hi2c1.Init.OwnAddress2Masks = I2C_OA2_NOMASK;
  hi2c1.Init.GeneralCallMode = I2C_GENERALCALL_DISABLE;
  hi2c1.Init.NoStretchMode = I2C_NOSTRETCH_DISABLE;
  if (HAL_I2C_Init(&hi2c1) != HAL_OK)
  {
    Error_Handler();
  }

  if (HAL_I2CEx_ConfigAnalogFilter(&hi2c1, I2C_ANALOGFILTER_ENABLE) != HAL_OK)
  {
    Error_Handler();
  }
}
```
## Program, który odczytuje dane z ACC i wyświetla je na konsoli:
```ruby
#include "stm32f7xx_hal.h"  // Dołącz bibliotekę HAL
#include "stdio.h"  // Dołącz bibliotekę standardowego wejścia/wyjścia
#define LIS3DSH_ADDRESS 0x1E  // Adres akcelerometru LIS3DSH

I2C_HandleTypeDef hi2c1;  // Struktura uchwytu dla interfejsu I2C

void SystemClock_Config(void);  // Funkcja konfigurująca zegar systemowy
void MX_I2C1_Init(void);  // Funkcja inicjalizująca interfejs I2C

int main(void)
{
  HAL_Init();  // Inicjalizuj HAL
  SystemClock_Config();  // Skonfiguruj zegar systemowy
  MX_I2C1_Init();  // Skonfiguruj interfejs I2C

  uint8_t data[6];  // Bufor na odczytane dane z akcelerometru

  while (1)
  {
    // Odczytaj dane z akcelerometru
    if (HAL_I2C_Master_Receive(&hi2c1, LIS3DSH_ADDRESS, data, 6, 1000) == HAL_OK)
    {
      // Wyświetl odczytane dane na konsoli
      int16_t x = (int16_t)(data[1] << 8 | data[0]);
      int16_t y = (int16_t)(data[3] << 8 | data[2]);
      int16_t z = (int16_t)(data[5] << 8 | data[4]);
      printf("X: %d, Y: %d, Z: %d\n", x, y, z);
    }
    else
    {
      printf("Błąd odczytu danych z akcelerometru!\n");
    }
    HAL_Delay(500);  // Zatrzymaj program na 500 ms
  }
}
```
# Deadline
10.02.2023
