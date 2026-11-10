# AdvancedMD-AutoEnter

Desktop helpers that speed up data entry in **AdvancedMD PM** on Windows. Each tool takes a
screenshot (or a PDF/image you pick), uses Google Gemini to read the patient details out of it,
and then types those details into the AdvancedMD form for you with `pyautogui`.

> **Disclaimer:** This project is not affiliated with or endorsed by AdvancedMD. Screenshots you
> process are sent to the Google Gemini API, so make sure that is allowed under your
> organization's HIPAA / data-handling policies before using it with real patient data.

## Tools

| Script | What it does |
| --- | --- |
| `PatientCreatePackage/PatientCreate` | Creates a new patient: reads name, DOB, MRN, sex, phone, address, city and ZIP from a screenshot, fills the *New Patient* form, picks the matching city from the dropdown, and stops if a "Possible Patient Duplicate" banner appears. |
| `ChargeChopped` | Quick charge entry: reads patient/visit info from a screenshot and fills the name, visit, facility and info fields. |
| `BatchChopped` | Older batch version of patient entry that works from a PDF or a folder of images. |

All three have a small Tkinter GUI (default) and a terminal mode (`--cli`). Press the `` ` ``
(backtick) key as a global hotkey once **Hotkey Mode** is turned on.

## Requirements

- Windows 10/11
- Python 3.10+ (python.org build, which includes Tkinter)
- A [Google Gemini API key](https://aistudio.google.com/app/apikey)
- [Poppler](https://github.com/oschwartz10612/poppler-windows/releases) on your `PATH` (only
  needed for PDF input)

## Setup

```powershell
git clone https://github.com/Heptagon2/AdvancedMD-AutoEnter.git
cd AdvancedMD-AutoEnter

python -m venv .venv
# If activation is blocked: Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate
pip install -r requirements.txt

copy .env.example .env   # then edit .env and paste in your Gemini API key
```

## Usage

```powershell
python ChargeChopped           # GUI
python ChargeChopped --cli     # terminal mode

cd PatientCreatePackage        # PatientCreate looks for its images and .env in the current folder
copy ..\.env .env
python PatientCreate
```

1. **Calibrate** first. The tool asks you to hover over each form field in AdvancedMD and saves
   the screen positions to `coordinates.json` / `chargeCoordinates.json` in the current folder (next to the exe when built).
   Calibration is per machine and screen layout, so these files are not committed.
2. Open the patient document on screen, put the mouse on that monitor, and trigger a capture
   (button or hotkey). Review the extracted fields, correct anything wrong, and let the tool type
   them in.

## Building an .exe (optional)

```powershell
pyinstaller --onefile --noconsole --hidden-import=mss --hidden-import=mss.tools `
  --add-data "PatientCreatePackage\ExampleOfWhiteSpace.png;." `
  --add-data "PatientCreatePackage\FlagTemplate.png;." `
  PatientCreatePackage\PatientCreate

pyinstaller --name PatientQuickCharge --onefile --noconsole --hidden-import=mss --hidden-import=mss.tools ChargeChopped
```

The executables land in `dist/`. **Do not bundle your `.env` into the exe** (`--add-data ".env;."`),
since anyone who has the exe could extract the key. Instead, place a `.env` file next to the exe.
The apps load it from there at startup.

## Project layout

```
PatientCreatePackage/
  PatientCreate            # new-patient entry tool
  FlagTemplate.png         # image of the duplicate-patient banner (template matching)
  ExampleOfWhiteSpace.png  # reference shown during calibration
ChargeChopped              # quick charge entry tool
BatchChopped               # batch PDF/image entry tool
requirements.txt
.env.example               # copy to .env and add your key
```
