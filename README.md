# STM32F401CCU6 Development Sandbox

Projekt demonstracyjny i baza do eksperymentów z mikrokontrolerem **STM32F401CCU6** (rdzeń ARM Cortex-M4F). Repozytorium zawiera warstwę startową aplikacji, sterowniki STM32 LL, własne sterowniki peryferiów, bootloader oraz moduły aplikacyjne oparte na FreeRTOS.

## Najważniejsze cechy

- mikrokontroler STM32F401xC, obudowa STM32F401CCU6,
- kompilacja przez `arm-none-eabi-gcc` i GNU Make,
- CMSIS oraz STM32F4 LL (Low-Layer Drivers),
- aplikacja i bootloader z osobnymi skryptami linkera,
- FreeRTOS z portem `ARM_CM4F` i sterownikiem pamięci `heap_2`,
- obsługa interfejsów UART, SPI, I2C, 1-Wire oraz RTC,
- przygotowane sterowniki dla wyświetlaczy, pamięci Flash, czujników i modułów radiowych.

## Wymagane narzędzia

- GNU Make,
- `arm-none-eabi-gcc`, `arm-none-eabi-objcopy`, `arm-none-eabi-objdump`,
- `g++` (sprawdzany przez system budowania),
- opcjonalnie Doxygen do generowania dokumentacji,
- opcjonalnie OpenOCD/ST-Link do programowania mikrokontrolera.

## Budowanie

Domyślna konfiguracja buduje aplikację, bootloader oraz połączony obraz HEX:

```sh
make
```

Dostępne cele:

```sh
make app       # tylko aplikacja
make sbl       # tylko bootloader
make           # aplikacja + bootloader + pliki HEX
make doc       # dokumentacja Doxygen
make clean     # czyszczenie artefaktów kompilacji
```

Parametry projektu są ustawione w głównym `Makefile`:

- `DEVICE := STM32F401xC`,
- `MACH := cortex-m4`,
- `FLOAT_ABI := hard`,
- `USE_SBL := yes`,
- `USE_RTOS := yes`,
- `FREERTOS_HEAP := heap_2`.

Artefakty budowania trafiają do katalogu `out/`.

## Sterowniki i biblioteki

### Sterowniki STM32

Katalog `Drivers/` zawiera:

- **CMSIS** – nagłówki rdzenia ARM Cortex-M oraz definicje urządzenia STM32F4,
- **STM32F4xx LL** – sterowniki Low-Layer dla:
	- ADC,
	- CRC,
	- DAC,
	- DMA i DMA2D,
	- EXTI,
	- FMPI2C,
	- GPIO,
	- I2C,
	- IWDG,
	- LPTIM,
	- PWR,
	- RCC,
	- RNG,
	- RTC,
	- SPI,
	- TIM,
	- USART,
	- WWDG,
	- Cortex i funkcje systemowe.

### Własne sterowniki urządzeń (`tools/Reuse`)

- **CC1101** – moduł radiowy Sub-GHz,
- **LoRa E32** – moduł LoRa E32,
- **nRF24L01** – komunikacja radiowa 2,4 GHz,
- **SI4432** – moduł radiowy Sub-GHz,
- **SH1106** – wyświetlacze OLED,
- **LCD12864** – wyświetlacze znakowo-graficzne, wraz z fontami 8/12/16/20/24 px,
- **WS25Qxx** – pamięci SPI Flash,
- **TM1637** – sterownik wyświetlaczy 7-segmentowych,
- **SCD41** – czujnik CO2,
- **SHT40** – czujnik temperatury i wilgotności,
- **DS18B20** – czujnik temperatury 1-Wire,
- **RTC** – obsługa zegara czasu rzeczywistego,
- **UART, SPI, I2C** – wspólne warstwy komunikacyjne,
- **1-Wire** – magistrala dla czujników 1-Wire,
- **CRC-8 i CRC-16** – funkcje kontroli integralności danych,
- **circular buffer** – bufor cykliczny,
- **log, beep, delay, device_info, hw_monitor** – narzędzia pomocnicze aplikacji.

## Moduły FreeRTOS (`tools/RTOS_MODULES`)

- `task_supervisor` – nadzór pracy systemu i zadań,
- `task_monitor` – monitorowanie stanu aplikacji,
- `task_cc1101` – zadanie komunikacji z CC1101,
- `task_scd41` – zadanie odczytu danych z SCD41,
- `rtos_hooks` – haki FreeRTOS,
- `runtime_stats_hw_timer` – statystyki czasu wykonania z użyciem timera sprzętowego,
- `FreeRTOSConfig.h` – konfiguracja FreeRTOS dla projektu.

FreeRTOS znajduje się w `tools/freertos/` i jest kompilowany z portem `GCC/ARM_CM4F`.

## Struktura katalogów

```text
Core/                         kod aplikacji i pliki startowe
Drivers/                      CMSIS oraz sterowniki STM32 LL
tools/Reuse/                  własne sterowniki i biblioteki wspólne
tools/RTOS_MODULES/           zadania i rozszerzenia FreeRTOS
tools/freertos/               kernel FreeRTOS
tools/bootloader/             kod bootloadera
tools/makefiles/              fragmenty systemu budowania
tools/docs/                   dokumentacja układów i peryferiów
tools/support/                skrypty pomocnicze, SVD i obsługa programowania
```

## Bootloader

Bootloader jest włączony domyślnie (`USE_SBL := yes`). Dla STM32F401xC używane są:

- `tools/bootloader/stm32f401ccux/`,
- `tools/STM32F401CCUX_FLASH_SBL.ld`,
- rozmiar obszaru bootloadera: `20 KiB`.

Skrypty linkera dla aplikacji i bootloadera znajdują się w `tools/`.

## Dokumentacja

Materiały referencyjne, datasheety oraz dokumentacja modułów znajdują się w `tools/docs/`. Dokumentację kodu można wygenerować poleceniem:

```sh
make doc
```

## Licencja

Kod projektu jest udostępniony jako open source. Zależności zewnętrzne zachowują własne pliki licencyjne, znajdujące się w odpowiednich katalogach, między innymi `Drivers/CMSIS/LICENSE.txt` i `Drivers/STM32F4xx_HAL_Driver/LICENSE.txt`.
