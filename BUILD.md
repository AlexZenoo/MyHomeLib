# Інструкція по збірці MyHomeLib

Це детальна інструкція з коментарями по збірці програми MyHomeLib з вихідних кодів до готового інсталятора.

## Зміст

1. [Системні вимоги](#системні-вимоги)
2. [Необхідні компоненти та інструменти](#необхідні-компоненти-та-інструменти)
3. [Підготовка середовища розробки](#підготовка-середовища-розробки)
4. [Структура проєкту](#структура-проєкту)
5. [Збірка компонентів](#збірка-компонентів)
6. [Збірка основної програми](#збірка-основної-програми)
7. [Підготовка файлів для інсталятора](#підготовка-файлів-для-інсталятора)
8. [Збірка стандартного інсталятора](#збірка-стандартного-інсталятора)
9. [Збірка портативної версії](#збірка-портативної-версії)
10. [Усунення помилок](#усунення-помилок)

---

## Системні вимоги

Для збірки MyHomeLib потрібна система Windows з наступними характеристиками:

- **ОС:** Windows 7 або новіша (рекомендується Windows 10/11)
- **Процесор:** 2 ГГц або швидший
- **ОЗП:** Мінімум 4 ГБ (рекомендується 8 ГБ або більше)
- **Вільне місце на диску:** Мінімум 10 ГБ для середовища розробки та збірки

---

## Необхідні компоненти та інструменти

### 1. Embarcadero RAD Studio / Delphi

MyHomeLib розробляється на Delphi. Потрібна версія:

- **Рекомендована версія:** Delphi 10.4 Sydney або новіша (ProjectVersion 19.5 в файлах `.dproj`)
- **Мінімальна версія:** Delphi XE2 або новіша

**Встановлення:**
1. Завантажте Embarcadero RAD Studio / Delphi з офіційного сайту Embarcadero
2. Запустіть інсталятор та виберіть компоненти:
   - ✅ Delphi Compiler (Win32 та Win64)
   - ✅ VCL Framework
   - ✅ Runtime Library (RTL)
3. Активуйте ліцензію (комерційна або Community Edition)

### 2. Сторонні бібліотеки Delphi

MyHomeLib використовує наступні бібліотеки компонентів:

#### Обов'язкові бібліотеки:

1. **FastMM5** - менеджер пам'яті
   - Завантаження: https://github.com/pleriche/FastMM5
   - Встановлення: Додайте шлях до папки FastMM5 в Library Path

2. **VirtualTreeView (VirtualTreesD12)** - компонент для відображення дерева
   - Завантаження: https://github.com/Virtual-TreeView/Virtual-TreeView
   - Встановлення: Скомпілюйте та встановіть пакет для вашої версії Delphi

3. **LMD Tools** - набір компонентів LMD (lmdrtl, lmdcore, lmdplugin)
   - Версія: Runtime для Delphi 12
   - Потрібні пакети: lmdrtlx9rt_120, lmdrtl9rt_120, lmdcore9rt_120, lmdplugin1rt_120

4. **ZipMaster (ZipmasterD12)** - робота з ZIP-архівами
   - Завантаження: https://github.com/ZipMaster/ZipMaster
   - Встановлення: Скомпілюйте пакет ZipmasterD12

5. **vclAbsDB** - компоненти для роботи з базами даних
   - Версія: для Delphi 12 (vclAbsDBd12)

6. **Indy** - компоненти для роботи з мережею
   - Зазвичай входить до складу Delphi (IndyCore, IndySystem, IndyProtocols)

7. **ZipForged** - альтернативна бібліотека для роботи з архівами
   - Потрібні пакети: dclZipForged11, vclZipForged12

**Налаштування Library Path:**

Після встановлення всіх бібліотек, додайте шляхи до них в Delphi:
1. Відкрийте: `Tools` → `Options` → `Language` → `Delphi` → `Library`
2. Для кожної платформи (Win32 та Win64) додайте шляхи до папок:
   - Вихідних кодів бібліотек
   - Скомпільованих DCU файлів

### 3. Inno Setup

Для створення інсталятора використовується Inno Setup:

- **Версія:** 6.0 або новіша
- **Завантаження:** https://jrsoftware.org/isdl.php

**Встановлення:**
1. Завантажте та запустіть інсталятор Inno Setup
2. Встановіть у зручне місце (наприклад, `C:\Program Files (x86)\Inno Setup 6`)
3. Додайте шлях до Inno Setup в змінну середовища PATH (опціонально, для збірки з командного рядка)

### 4. Необхідні DLL бібліотеки

MyHomeLib потребує наступних бінарних бібліотек:

#### Для 32-bit версії (потрібно розмістити в `Installer\x86\`):
- **sqlite3.dll** - SQLite database engine (32-bit)
- **libeay32.dll** - OpenSSL криптографічна бібліотека (32-bit)
- **ssleay32.dll** - OpenSSL SSL/TLS бібліотека (32-bit)

#### Для 64-bit версії (потрібно розмістити в `Installer\x64\`):
- **sqlite3.dll** - SQLite database engine (64-bit)
- **libeay32.dll** - OpenSSL криптографічна бібліотека (64-bit)
- **ssleay32.dll** - OpenSSL SSL/TLS бібліотека (64-bit)

**Де взяти DLL:**
- SQLite: https://www.sqlite.org/download.html (завантажте precompiled binaries)
- OpenSSL: https://slproweb.com/products/Win32OpenSSL.html (або скомпілюйте самостійно)

### 5. Додаткові файли

Потрібно підготувати папку `Installer\Common\` з наступними файлами та підпапками:

```
Installer/Common/
├── License.txt               # Текст ліцензії
├── MyHomeLib.chm            # Файл довідки (CHM Help)
├── MyHomeLib.url            # Посилання на сайт проєкту
├── genres_fb2.glst          # Список жанрів FB2
├── genres_nonfb2.glst       # Список жанрів для інших форматів
├── collections.ini          # Конфігурація колекцій
├── AlReader/                # Папка з AlReader
│   ├── AlReader2/          # Підпапка AlReader2
│   └── ...
└── converters/              # Конвертери форматів
    ├── fb2lrf/             # FB2 в LRF
    ├── fb2pdf/             # FB2 в PDF
    ├── fb2epub/            # FB2 в EPUB
    └── fb2mobi/            # FB2 в MOBI
```

---

## Підготовка середовища розробки

### Крок 1: Клонування репозиторію

```bash
git clone https://github.com/AlexZenoo/MyHomeLib.git
cd MyHomeLib
```

### Крок 2: Перевірка структури

Переконайтеся, що у вас є такі основні папки:

```
MyHomeLib/
├── Program/           # Основний код програми
├── Components/        # Користувацькі компоненти
├── Installer/         # Скрипти та ресурси для інсталятора
└── Utils/            # Допоміжні утиліти
```

### Крок 3: Створення папок для збірки

Створіть необхідні папки для DLL бібліотек, якщо їх немає:

```bash
mkdir -p Installer\x86
mkdir -p Installer\x64
mkdir -p Installer\Common
mkdir -p Installer\Out
```

### Крок 4: Розміщення DLL бібліотек

Скопіюйте завантажені DLL файли:

- Файли для 32-bit в `Installer\x86\`
- Файли для 64-bit в `Installer\x64\`

### Крок 5: Підготовка Common файлів

Розмістіть або створіть всі необхідні файли в папці `Installer\Common\` (див. розділ "Додаткові файли" вище).

---

## Структура проєкту

### Основні компоненти:

#### 1. **Program/** - Головна програма
- **MyHomeLib.dpr** - головний файл проєкту
- **MyhomeLib.dproj** - файл проєкту Delphi (XML)
- **MHL.groupproj** - груповий проєкт (включає всі модулі)
- **Forms/** - форми інтерфейсу користувача
- **Units/** - основні модулі програми
- **DAO/** - Data Access Objects (робота з базами даних)
- **ImportImpl/** - реалізація імпорту
- **DwnldImpl/** - реалізація завантаження
- **UtilsImpl/** - допоміжні утиліти
- **Wizards/** - майстри (wizards)

#### 2. **Components/MHLComponents/** - Користувацькі компоненти
- **MHLComponents.dpk** - пакет компонентів
- **MHLComponents.dproj** - проєкт пакета

#### 3. **Utils/** - Допоміжні утиліти
- **MHLSQLiteConsole/** - консольна утиліта для SQLite
- **MHLSQLiteExt/** - розширення SQLite

#### 4. **Installer/** - Інсталятор
- **Setup_Script_MyHomeLib.iss** - скрипт для 32-bit інсталятора
- **Setup_Script_MyHomeLib_x64.iss** - скрипт для 64-bit інсталятора
- **Common.iss** - спільні налаштування обох інсталяторів

---

## Збірка компонентів

### Збірка MHLComponents

Спочатку потрібно зібрати та встановити користувацькі компоненти.

**З IDE:**

1. Відкрийте Delphi
2. Відкрийте файл `Components\MHLComponents\MHLComponents.dproj`
3. Виберіть конфігурацію збірки:
   - **Release** (для фінальної збірки)
   - **Debug** (для налагодження)
4. Виберіть платформу:
   - **Win32** (32-bit)
   - **Win64** (64-bit)
5. Натисніть `Project` → `Build MHLComponents`
6. Натисніть `Component` → `Install Packages` та перевірте, що пакет встановлено

**З командного рядка:**

```bash
# Для 32-bit
cd Components\MHLComponents
msbuild MHLComponents.dproj /p:Config=Release /p:Platform=Win32

# Для 64-bit
msbuild MHLComponents.dproj /p:Config=Release /p:Platform=Win64
```

**Примітка:** Після збірки DCU та BPL файли будуть створені в теці проєкту або в загальній теці бібліотек Delphi.

---

## Збірка основної програми

### Збірка з IDE (рекомендований спосіб)

1. **Відкрийте груповий проєкт:**
   - Запустіть Delphi
   - Відкрийте `Program\MHL.groupproj`
   - Це завантажить всі пов'язані проєкти

2. **Виберіть конфігурацію збірки:**
   - У Project Manager клацніть правою кнопкою на `MyHomeLib.exe`
   - Виберіть `Build Configurations` → `Release`

3. **Збірка для Win32 (32-bit):**
   - Виберіть платформу: `Win32`
   - Натисніть `Project` → `Build MyHomeLib`
   - Або натисніть `Shift+F9`
   - Результат: `Program\Out\Bin\MyHomeLib.exe`

4. **Збірка для Win64 (64-bit):**
   - Виберіть платформу: `Win64`
   - Натисніть `Project` → `Build MyHomeLib`
   - Результат: `Program\Out\Bin64\MyHomeLib.exe`

**Важливо:**
- Переконайтеся, що обрано конфігурацію **Release** для фінальної збірки
- Вихідні папки для виконуваних файлів:
  - Win32: `Program\Out\Bin\`
  - Win64: `Program\Out\Bin64\`

### Збірка з командного рядка

Для автоматизації процесу збірки можна використовувати MSBuild:

```bash
cd Program

# Збірка 32-bit Release
msbuild MyhomeLib.dproj /p:Config=Release /p:Platform=Win32

# Збірка 64-bit Release
msbuild MyhomeLib.dproj /p:Config=Release /p:Platform=Win64

# Збірка обох платформ
msbuild MyhomeLib.dproj /p:Config=Release /p:Platform=Win32
msbuild MyhomeLib.dproj /p:Config=Release /p:Platform=Win64
```

**Альтернативно через rsvars.bat:**

```bash
# Встановіть змінні середовища Delphi
call "C:\Program Files (x86)\Embarcadero\Studio\22.0\bin\rsvars.bat"

# Збірка
cd Program
msbuild MyhomeLib.dproj /p:Config=Release /p:Platform=Win32
```

**Примітка:** Шлях до `rsvars.bat` залежить від встановленої версії Delphi (22.0 - це приклад для Delphi 11 Alexandria).

### Перевірка успішності збірки

Після завершення збірки перевірте:

1. Наявність виконуваного файлу:
   - `Program\Out\Bin\MyHomeLib.exe` (для Win32)
   - `Program\Out\Bin64\MyHomeLib.exe` (для Win64)

2. Розмір файлу (має бути кілька МБ)

3. Відсутність помилок у вікні Messages в Delphi IDE

4. Спробуйте запустити виконуваний файл для перевірки працездатності

---

## Підготовка файлів для інсталятора

Перед збіркою інсталятора переконайтеся, що всі необхідні файли на місці:

### Контрольний список файлів:

```
✅ Program\Out\Bin\MyHomeLib.exe          # 32-bit виконуваний файл
✅ Program\Out\Bin64\MyHomeLib.exe        # 64-bit виконуваний файл
✅ Installer\x86\sqlite3.dll              # SQLite 32-bit
✅ Installer\x86\libeay32.dll             # OpenSSL 32-bit
✅ Installer\x86\ssleay32.dll             # OpenSSL 32-bit
✅ Installer\x64\sqlite3.dll              # SQLite 64-bit
✅ Installer\x64\libeay32.dll             # OpenSSL 64-bit
✅ Installer\x64\ssleay32.dll             # OpenSSL 64-bit
✅ Installer\Common\License.txt           # Ліцензія
✅ Installer\Common\MyHomeLib.chm         # Довідка
✅ Installer\Common\MyHomeLib.url         # Посилання
✅ Installer\Common\genres_fb2.glst       # Жанри FB2
✅ Installer\Common\genres_nonfb2.glst    # Жанри non-FB2
✅ Installer\Common\collections.ini       # Конфігурація
✅ Installer\Common\AlReader\             # Папка AlReader
✅ Installer\Common\converters\           # Папка конвертерів
```

### Перевірка скриптів:

1. Відкрийте `Installer\Setup_Script_MyHomeLib.iss` та перевірте:
   - `SourceFolder` вказує на `'..\Program\Out\Bin\'`
   - `LibFolder` встановлено в `'x86\'`

2. Відкрийте `Installer\Setup_Script_MyHomeLib_x64.iss` та перевірте:
   - `SourceFolder` вказує на `'..\Program\Out\Bin64\'`
   - `LibFolder` встановлено в `'x64\'`

---

## Збірка стандартного інсталятора

### Збірка 32-bit інсталятора

**З Inno Setup IDE:**

1. Запустіть Inno Setup Compiler
2. Відкрийте `File` → `Open` → виберіть `Installer\Setup_Script_MyHomeLib.iss`
3. Натисніть `Build` → `Compile` або натисніть `Ctrl+F9`
4. Дочекайтеся завершення компіляції
5. Знайдіть готовий інсталятор: `Installer\Out\Setup_MyHomeLib_X.Y.Z.exe`
   - X.Y.Z - версія програми, яка береться з версійної інформації `MyHomeLib.exe`

**З командного рядка:**

```bash
cd Installer

# Компіляція 32-bit інсталятора
"C:\Program Files (x86)\Inno Setup 6\ISCC.exe" Setup_Script_MyHomeLib.iss
```

### Збірка 64-bit інсталятора

**З Inno Setup IDE:**

1. Запустіть Inno Setup Compiler
2. Відкрийте `File` → `Open` → виберіть `Installer\Setup_Script_MyHomeLib_x64.iss`
3. Натисніть `Build` → `Compile` або натисніть `Ctrl+F9`
4. Дочекайтеся завершення компіляції
5. Знайдіть готовий інсталятор: `Installer\Out\Setup_MyHomeLib_X.Y.Z_x64.exe`

**З командного рядка:**

```bash
cd Installer

# Компіляція 64-bit інсталятора
"C:\Program Files (x86)\Inno Setup 6\ISCC.exe" Setup_Script_MyHomeLib_x64.iss
```

### Збірка обох інсталяторів автоматично

Створіть BAT файл `Installer\BuildAll.bat`:

```batch
@echo off
echo Building MyHomeLib Installers...
echo.

set ISCC="C:\Program Files (x86)\Inno Setup 6\ISCC.exe"

echo Building 32-bit installer...
%ISCC% Setup_Script_MyHomeLib.iss
if errorlevel 1 goto error

echo.
echo Building 64-bit installer...
%ISCC% Setup_Script_MyHomeLib_x64.iss
if errorlevel 1 goto error

echo.
echo All installers built successfully!
echo Output directory: Out\
goto end

:error
echo.
echo ERROR: Build failed!
exit /b 1

:end
pause
```

Запустіть:
```bash
cd Installer
BuildAll.bat
```

### Перевірка інсталятора

Після успішної збірки:

1. Перевірте наявність файлів в `Installer\Out\`:
   - `Setup_MyHomeLib_X.Y.Z.exe` (32-bit)
   - `Setup_MyHomeLib_X.Y.Z_x64.exe` (64-bit)

2. Перевірте розмір файлів (зазвичай 10-50 МБ в залежності від вмісту)

3. Запустіть інсталятор на тестовій системі:
   - Перевірте процес встановлення
   - Перевірте створення ярликів
   - Перевірте запуск програми після встановлення

---

## Збірка портативної версії

Портативна версія не потребує встановлення і може запускатися з будь-якого місця (USB-флешки, мережевого диску тощо).

### Підготовка портативної збірки

#### Метод 1: Ручне створення (рекомендовано)

1. **Створіть папку для портативної версії:**

```bash
mkdir MyHomeLib_Portable
cd MyHomeLib_Portable
```

2. **Скопіюйте виконуваний файл:**

Для 32-bit версії:
```bash
copy ..\Program\Out\Bin\MyHomeLib.exe .
```

Для 64-bit версії:
```bash
copy ..\Program\Out\Bin64\MyHomeLib.exe .
```

3. **Скопіюйте необхідні DLL бібліотеки:**

Для 32-bit версії:
```bash
copy ..\Installer\x86\sqlite3.dll .
copy ..\Installer\x86\libeay32.dll .
copy ..\Installer\x86\ssleay32.dll .
```

Для 64-bit версії:
```bash
copy ..\Installer\x64\sqlite3.dll .
copy ..\Installer\x64\libeay32.dll .
copy ..\Installer\x64\ssleay32.dll .
```

4. **Скопіюйте додаткові файли:**

```bash
# Довідка та документація
copy ..\Installer\Common\MyHomeLib.chm .
copy ..\Installer\Common\License.txt .
copy ..\Installer\Common\MyHomeLib.url .

# Файли жанрів
copy ..\Installer\Common\genres_fb2.glst .
copy ..\Installer\Common\genres_nonfb2.glst .

# AlReader
xcopy ..\Installer\Common\AlReader AlReader\ /E /I

# Конвертери
xcopy ..\Installer\Common\converters converters\ /E /I
```

5. **Створіть файл для ідентифікації портативного режиму:**

Деякі програми визначають портативний режим через наявність спеціального файлу або налаштування. Створіть порожній файл `portable.dat` або файл налаштувань:

```bash
echo. > portable.dat
```

Або створіть `MyHomeLib.ini` з вмістом:
```ini
[Settings]
Portable=1
```

6. **Структура портативної версії:**

Після всіх кроків у вас має бути така структура:

```
MyHomeLib_Portable/
├── MyHomeLib.exe           # Основний виконуваний файл
├── sqlite3.dll             # SQLite база даних
├── libeay32.dll            # OpenSSL
├── ssleay32.dll            # OpenSSL
├── MyHomeLib.chm           # Довідка
├── License.txt             # Ліцензія
├── MyHomeLib.url           # Посилання
├── genres_fb2.glst         # Жанри FB2
├── genres_nonfb2.glst      # Жанри non-FB2
├── portable.dat            # Маркер портативного режиму (опціонально)
├── AlReader/               # Папка AlReader
│   └── ...
└── converters/             # Папка конвертерів
    ├── fb2lrf/
    ├── fb2pdf/
    ├── fb2epub/
    └── fb2mobi/
```

#### Метод 2: Автоматизація через скрипт

Створіть файл `BuildPortable.bat` в кореневій папці проєкту:

```batch
@echo off
setlocal

:: Назва портативної версії
set PORTABLE_DIR=MyHomeLib_Portable_Win32
set PORTABLE_DIR_X64=MyHomeLib_Portable_Win64

:: Джерела файлів
set SRC_BIN=Program\Out\Bin
set SRC_BIN64=Program\Out\Bin64
set SRC_COMMON=Installer\Common
set SRC_X86=Installer\x86
set SRC_X64=Installer\x64

echo ========================================
echo Building MyHomeLib Portable Versions
echo ========================================
echo.

:: ===== 32-bit Portable =====
echo Creating 32-bit portable version...

if exist %PORTABLE_DIR% rd /s /q %PORTABLE_DIR%
mkdir %PORTABLE_DIR%

:: Копіювання виконуваного файлу
copy "%SRC_BIN%\MyHomeLib.exe" "%PORTABLE_DIR%\" > nul

:: Копіювання DLL
copy "%SRC_X86%\sqlite3.dll" "%PORTABLE_DIR%\" > nul
copy "%SRC_X86%\libeay32.dll" "%PORTABLE_DIR%\" > nul
copy "%SRC_X86%\ssleay32.dll" "%PORTABLE_DIR%\" > nul

:: Копіювання додаткових файлів
copy "%SRC_COMMON%\MyHomeLib.chm" "%PORTABLE_DIR%\" > nul
copy "%SRC_COMMON%\License.txt" "%PORTABLE_DIR%\" > nul
copy "%SRC_COMMON%\MyHomeLib.url" "%PORTABLE_DIR%\" > nul
copy "%SRC_COMMON%\genres_fb2.glst" "%PORTABLE_DIR%\" > nul
copy "%SRC_COMMON%\genres_nonfb2.glst" "%PORTABLE_DIR%\" > nul

:: Копіювання папок
xcopy "%SRC_COMMON%\AlReader" "%PORTABLE_DIR%\AlReader\" /E /I /Q > nul
xcopy "%SRC_COMMON%\converters" "%PORTABLE_DIR%\converters\" /E /I /Q > nul

:: Створення маркера портативного режиму
echo. > "%PORTABLE_DIR%\portable.dat"

echo 32-bit portable version created: %PORTABLE_DIR%
echo.

:: ===== 64-bit Portable =====
echo Creating 64-bit portable version...

if exist %PORTABLE_DIR_X64% rd /s /q %PORTABLE_DIR_X64%
mkdir %PORTABLE_DIR_X64%

:: Копіювання виконуваного файлу
copy "%SRC_BIN64%\MyHomeLib.exe" "%PORTABLE_DIR_X64%\" > nul

:: Копіювання DLL
copy "%SRC_X64%\sqlite3.dll" "%PORTABLE_DIR_X64%\" > nul
copy "%SRC_X64%\libeay32.dll" "%PORTABLE_DIR_X64%\" > nul
copy "%SRC_X64%\ssleay32.dll" "%PORTABLE_DIR_X64%\" > nul

:: Копіювання додаткових файлів
copy "%SRC_COMMON%\MyHomeLib.chm" "%PORTABLE_DIR_X64%\" > nul
copy "%SRC_COMMON%\License.txt" "%PORTABLE_DIR_X64%\" > nul
copy "%SRC_COMMON%\MyHomeLib.url" "%PORTABLE_DIR_X64%\" > nul
copy "%SRC_COMMON%\genres_fb2.glst" "%PORTABLE_DIR_X64%\" > nul
copy "%SRC_COMMON%\genres_nonfb2.glst" "%PORTABLE_DIR_X64%\" > nul

:: Копіювання папок
xcopy "%SRC_COMMON%\AlReader" "%PORTABLE_DIR_X64%\AlReader\" /E /I /Q > nul
xcopy "%SRC_COMMON%\converters" "%PORTABLE_DIR_X64%\converters\" /E /I /Q > nul

:: Створення маркера портативного режиму
echo. > "%PORTABLE_DIR_X64%\portable.dat"

echo 64-bit portable version created: %PORTABLE_DIR_X64%
echo.

echo ========================================
echo Portable versions created successfully!
echo ========================================
pause
```

Запуск скрипта:
```bash
BuildPortable.bat
```

### Створення ZIP-архіву портативної версії

Для зручного розповсюдження можна запакувати портативну версію в ZIP:

**Використовуючи 7-Zip:**

```bash
# 32-bit
"C:\Program Files\7-Zip\7z.exe" a -tzip MyHomeLib_Portable_Win32.zip MyHomeLib_Portable_Win32\*

# 64-bit
"C:\Program Files\7-Zip\7z.exe" a -tzip MyHomeLib_Portable_Win64.zip MyHomeLib_Portable_Win64\*
```

**Використовуючи PowerShell:**

```powershell
# 32-bit
Compress-Archive -Path MyHomeLib_Portable_Win32\* -DestinationPath MyHomeLib_Portable_Win32.zip -Force

# 64-bit
Compress-Archive -Path MyHomeLib_Portable_Win64\* -DestinationPath MyHomeLib_Portable_Win64.zip -Force
```

### Тестування портативної версії

1. **Розпакуйте архів на USB флешку або в тимчасову папку**

2. **Запустіть MyHomeLib.exe**

3. **Перевірте:**
   - ✅ Програма запускається без встановлення
   - ✅ Налаштування зберігаються в папці програми (не в реєстрі або AppData)
   - ✅ Бази даних створюються в папці програми
   - ✅ Всі функції працюють (імпорт, експорт, конвертація)

4. **Перевірте роботу на іншому комп'ютері**

### Відмінності портативної версії від встановленої

| Характеристика | Встановлена версія | Портативна версія |
|----------------|-------------------|-------------------|
| Встановлення | Потрібен інсталятор | Не потрібно |
| Розташування налаштувань | `%AppData%\MyHomeLib` | Папка програми |
| Розташування баз даних | `%AppData%\MyHomeLib\Data` | Папка програми |
| Ярлики в меню | Створюються | Не створюються |
| Деінсталяція | Через панель керування | Видалення папки |
| Запуск з USB | Вимагає встановлення | Так, безпосередньо |

---

## Усунення помилок

### Помилки компіляції Delphi

#### Помилка: "Unit XXX not found"

**Причина:** Відсутня бібліотека або неправильно налаштований Library Path.

**Рішення:**
1. Встановіть відсутню бібліотеку
2. Додайте шлях до бібліотеки в `Tools` → `Options` → `Library Path`
3. Перезапустіть Delphi IDE

#### Помилка: "Package XXX cannot be installed"

**Причина:** Несумісність версії пакета з версією Delphi.

**Рішення:**
1. Завантажте версію пакета, сумісну з вашим Delphi
2. Перекомпілюйте пакет з вихідних кодів для вашої версії
3. Перевірте залежності пакета

#### Помилка: "Out of memory"

**Причина:** Недостатньо пам'яті для компіляції великого проєкту.

**Рішення:**
1. Закрийте інші програми
2. Збільште файл підкачки Windows
3. Використовуйте 64-bit версію Delphi IDE (якщо доступна)
4. Розділіть збірку на етапи (спочатку компоненти, потім основна програма)

### Помилки Inno Setup

#### Помилка: "Source file not found"

**Причина:** Inno Setup не може знайти вихідний файл.

**Рішення:**
1. Перевірте наявність файлу `Program\Out\Bin\MyHomeLib.exe`
2. Переконайтеся, що проєкт успішно зібрався
3. Перевірте правильність шляхів в `.iss` файлі

#### Помилка: "Cannot open file"

**Причина:** Файл використовується іншим процесом або немає прав доступу.

**Рішення:**
1. Закрийте MyHomeLib.exe, якщо він запущений
2. Вимкніть антивірус тимчасово
3. Запустіть Inno Setup від імені адміністратора

#### Помилка при зчитуванні версії: "GetVersionComponents failed"

**Причина:** Відсутня версійна інформація в MyHomeLib.exe або файл пошкоджений.

**Рішення:**
1. Перезберіть проєкт з увімкненою версійною інформацією (`VerInfo_IncludeVerInfo=true`)
2. Перевірте налаштування Version Info в проєкті Delphi
3. Використайте фіксовану версію в скрипті замість `GetVersionComponents`

### Помилки запуску

#### Помилка: "MSVCP140.dll not found" або подібні

**Причина:** Відсутні Microsoft Visual C++ Redistributables.

**Рішення:**
1. Встановіть Microsoft Visual C++ Redistributable (x86 та/або x64)
2. Завантаження: https://aka.ms/vs/17/release/vc_redist.x86.exe та vc_redist.x64.exe

#### Помилка: "sqlite3.dll not found"

**Причина:** Відсутня DLL бібліотека SQLite.

**Рішення:**
1. Переконайтеся, що `sqlite3.dll` знаходиться в папці з `MyHomeLib.exe`
2. Для 32-bit використовуйте 32-bit DLL
3. Для 64-bit використовуйте 64-bit DLL

#### Програма не запускається без помилок

**Причина:** Можливо конфлікт DLL версій або відсутні залежності.

**Рішення:**
1. Використайте Dependency Walker для перевірки залежностей: http://www.dependencywalker.com/
2. Перевірте журнал подій Windows (Event Viewer)
3. Запустіть з налагодженням в Delphi для виявлення помилки

### Помилки портативної версії

#### Налаштування не зберігаються

**Причина:** Програма намагається зберегти налаштування в %AppData%, але немає прав доступу.

**Рішення:**
1. Переконайтеся, що папка програми має права на запис
2. Запустіть програму НЕ від імені адміністратора (для збереження в папку програми)
3. Перевірте наявність файлу-маркера портативного режиму

#### Програма не визначає портативний режим

**Причина:** Відсутній маркер портативного режиму або неправильна логіка визначення.

**Рішення:**
1. Створіть файл `portable.dat` в папці програми
2. Або створіть `MyHomeLib.ini` з параметром `Portable=1`
3. Перевірте вихідний код програми для розуміння логіки визначення портативного режиму

---

## Автоматизація повного процесу збірки

Для повної автоматизації можна створити майстер-скрипт `BuildAll.bat`:

```batch
@echo off
setlocal enabledelayedexpansion

echo =============================================
echo MyHomeLib Complete Build Process
echo =============================================
echo.

:: Налаштування шляхів
set DELPHI_BDS=C:\Program Files (x86)\Embarcadero\Studio\22.0
set INNO_SETUP=C:\Program Files (x86)\Inno Setup 6
set PROJECT_ROOT=%CD%

:: Встановлення змінних середовища Delphi
call "%DELPHI_BDS%\bin\rsvars.bat"

:: ===== Крок 1: Збірка компонентів =====
echo [1/6] Building MHLComponents...
cd Components\MHLComponents
msbuild MHLComponents.dproj /p:Config=Release /p:Platform=Win32 /t:Build /v:minimal
if errorlevel 1 goto error
msbuild MHLComponents.dproj /p:Config=Release /p:Platform=Win64 /t:Build /v:minimal
if errorlevel 1 goto error
cd "%PROJECT_ROOT%"
echo     MHLComponents built successfully
echo.

:: ===== Крок 2: Збірка основної програми Win32 =====
echo [2/6] Building MyHomeLib Win32...
cd Program
msbuild MyhomeLib.dproj /p:Config=Release /p:Platform=Win32 /t:Build /v:minimal
if errorlevel 1 goto error
cd "%PROJECT_ROOT%"
echo     MyHomeLib Win32 built successfully
echo.

:: ===== Крок 3: Збірка основної програми Win64 =====
echo [3/6] Building MyHomeLib Win64...
cd Program
msbuild MyhomeLib.dproj /p:Config=Release /p:Platform=Win64 /t:Build /v:minimal
if errorlevel 1 goto error
cd "%PROJECT_ROOT%"
echo     MyHomeLib Win64 built successfully
echo.

:: ===== Крок 4: Збірка інсталяторів =====
echo [4/6] Building installers...
cd Installer
"%INNO_SETUP%\ISCC.exe" Setup_Script_MyHomeLib.iss
if errorlevel 1 goto error
"%INNO_SETUP%\ISCC.exe" Setup_Script_MyHomeLib_x64.iss
if errorlevel 1 goto error
cd "%PROJECT_ROOT%"
echo     Installers built successfully
echo.

:: ===== Крок 5: Збірка портативних версій =====
echo [5/6] Building portable versions...
call BuildPortable.bat
if errorlevel 1 goto error
echo     Portable versions built successfully
echo.

:: ===== Крок 6: Архівація портативних версій =====
echo [6/6] Creating portable archives...
if exist "C:\Program Files\7-Zip\7z.exe" (
    "C:\Program Files\7-Zip\7z.exe" a -tzip MyHomeLib_Portable_Win32.zip MyHomeLib_Portable_Win32\* > nul
    "C:\Program Files\7-Zip\7z.exe" a -tzip MyHomeLib_Portable_Win64.zip MyHomeLib_Portable_Win64\* > nul
    echo     Archives created successfully
) else (
    echo     7-Zip not found, skipping archiving
)
echo.

:: ===== Завершення =====
echo =============================================
echo Build completed successfully!
echo =============================================
echo.
echo Output files:
echo   - Installer\Out\Setup_MyHomeLib_*.exe
echo   - MyHomeLib_Portable_Win32\
echo   - MyHomeLib_Portable_Win64\
echo   - MyHomeLib_Portable_Win32.zip
echo   - MyHomeLib_Portable_Win64.zip
echo.
goto end

:error
echo.
echo =============================================
echo ERROR: Build failed!
echo =============================================
exit /b 1

:end
pause
```

**Використання:**
```bash
BuildAll.bat
```

Цей скрипт виконає всі кроки збірки автоматично, від компонентів до готових інсталяторів та портативних версій.

---

## Додаткові поради

### Оптимізація розміру виконуваного файлу

Для зменшення розміру MyHomeLib.exe:

1. **Вимкніть налагоджувальну інформацію:**
   - Project Options → Compiling → Debugging → Debug information = False

2. **Увімкніть оптимізацію:**
   - Project Options → Compiling → Compiling → Optimization = True

3. **Вимкніть Map-файл:**
   - Project Options → Linking → Map file = Off

4. **Використовуйте UPX для стискання:**
   - Завантажте UPX: https://upx.github.io/
   - Запустіть: `upx --best MyHomeLib.exe`
   - **Увага:** Деякі антивіруси можуть помилково визначати стиснуті файли як шкідливі

### Цифровий підпис

Для підвищення довіри користувачів підпишіть виконуваний файл цифровим сертифікатом:

```bash
signtool sign /f MyCertificate.pfx /p password /t http://timestamp.digicert.com MyHomeLib.exe
```

### Контроль версій

Перед випуском оновіть версію в:
1. `Program\MyhomeLib.dproj` (VerInfo_Build, VerInfo_Release)
2. Resource файлі проєкту
3. Документації та README

### Тестування

Перед публічним релізом протестуйте:
- ✅ Встановлення на чисту систему Windows
- ✅ Оновлення з попередньої версії
- ✅ Запуск на різних версіях Windows (7, 10, 11)
- ✅ Портативну версію на USB флешці
- ✅ Всі основні функції програми
- ✅ Деінсталяцію

---

## Висновок

Ця інструкція охоплює повний цикл збірки MyHomeLib від вихідних кодів до готових інсталяторів та портативних версій.

**Основні кроки:**
1. ✅ Встановлення Delphi та необхідних бібліотек
2. ✅ Збірка компонентів MHLComponents
3. ✅ Збірка MyHomeLib.exe (Win32 та Win64)
4. ✅ Створення інсталяторів через Inno Setup
5. ✅ Підготовка портативних версій

Для питань та допомоги звертайтеся до репозиторію проєкту на GitHub.

**Корисні посилання:**
- Репозиторій MyHomeLib: https://github.com/AlexZenoo/MyHomeLib
- Embarcadero Delphi: https://www.embarcadero.com/products/delphi
- Inno Setup: https://jrsoftware.org/isinfo.php
- FastMM5: https://github.com/pleriche/FastMM5
- VirtualTreeView: https://github.com/Virtual-TreeView/Virtual-TreeView

---

**Останнє оновлення:** 2025-11-16
