# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-file Indian SIP (Systematic Investment Plan) calculator built with vanilla HTML, CSS, and JavaScript. The entire application is contained in `SIP_Calculator_India.html` - no build tools, dependencies, or package managers are required.

## Development Workflow

### Running the Application
Simply open `SIP_Calculator_India.html` in any modern web browser. No local server required.

### Making Changes
1. Edit `SIP_Calculator_India.html` directly
2. Refresh the browser to see changes
3. Test all calculation modes (corpus, SIP, years, rate)
4. Verify theme switching and settings persistence work correctly

### Testing Checklist
When making changes, manually verify:
- **Calculation modes**: Switch between "Target corpus", "Monthly SIP", "Years", and "Expected return" modes
- **Live updates**: Changes to sliders and inputs update calculations immediately
- **Step-up calculations**: Test "None", "Percent (%)", and "Fixed ₹" step-up types
- **Inflation adjustment**: Toggle between nominal and real returns
- **Theme persistence**: Dark/light theme should persist across page reloads (localStorage)
- **Settings persistence**: Last used values should restore on page load
- **Preset management**: Save, load, and delete presets
- **Table scrolling**: Monthly breakdown table should scroll correctly in the resizable container
- **Downloads**: CSV and Excel exports should include all monthly breakdown data
- **Responsive design**: Test at mobile (< 680px), tablet (< 1100px), and desktop widths

## Architecture

### Single-File Structure
The file is organized into three main sections:
1. **CSS Styles** (lines ~7-301): Theme tokens, responsive grid system, widget styles, table styling
2. **HTML Markup** (lines 304-605): Widget-based input system, resizable output panel, settings panel
3. **JavaScript** (lines 607-1320): Financial calculations, UI state management, localStorage persistence

### Key Components

#### Financial Engine
- `simulate()`: Core SIP calculation with month-by-month accumulation
- `runModel()`: Applies inflation adjustment wrapper
- `solveMonthlyForCorpus()`, `solveYearsForCorpus()`, `solveRateForCorpus()`: Numerical solvers using binary search (60-70 iterations, Δ ≤ 0.01% precision)

#### Widget System
Input fields are organized as grid-based widgets (lines 330-435). Each widget is a self-contained `.input-widget` div with consistent styling and hover effects.

#### State Management
All state is managed via localStorage with these keys:
- `sip_theme`: Dark/light theme preference
- `sip_settings_open`: Whether settings panel is expanded
- `sip_last_settings`: Most recent input values
- `sip_saved_presets`: Named preset configurations (JSON object)
- `sip_user_defaults`: User-configured default values
- `sip_output_width`, `sip_output_height`: Resizable output panel dimensions

#### Currency Formatting
Uses `Intl.NumberFormat('en-IN')` for Indian numbering (lakhs/crores). The `toIndianShort()` function formats large numbers as "₹ X.XX Cr" or "₹ X.XX L".

### Calculation Logic

**Step-up types:**
- **Percent**: Compounds yearly (e.g., 10% means year 1 = base SIP, year 2 = base × 1.1, year 3 = base × 1.21)
- **Fixed ₹**: Adds fixed amount yearly (e.g., ₹5000 means year 1 = base SIP, year 2 = base + 5000, year 3 = base + 10000)

**Inflation adjustment:**
When enabled, real return = (1 + nominal) / (1 + inflation) - 1

**Monthly compounding:**
All calculations use monthly compounding: monthlyRate = (1 + annualRate)^(1/12) - 1

**Step-up timing:**
Step-ups apply at months 13, 25, 37, etc. (each 12-month boundary).

## Common Modifications

### Adding a New Input Widget
1. Add HTML widget div in the `.widget-container` (line 330+)
2. Create corresponding UI reference in `const ui` (line 740+)
3. Add to input event listeners array (line 947+)
4. Update `readInputs()` to capture the value (line 799+)
5. Update settings persistence functions if needed (lines 1062+)

### Modifying Calculation Logic
1. Core simulation is in `simulate()` function (line 668)
2. Month-by-month loop handles contributions, growth, and step-ups
3. Schedule array tracks all monthly details for the breakdown table
4. Test with edge cases: 0% returns, negative inflation adjustment, 200 years

### Changing Theme Colors
Edit CSS custom properties in `:root` (dark theme, lines 9-13) and `[data-theme="light"]` (lines 14-18).

### Adjusting Numerical Solver Precision
Modify the convergence threshold in solver functions (lines 718, 726, 734):
```javascript
if(Math.abs(v-target)<=Math.max(1,target)*1e-4) // Current: 0.01%
```

## Important Notes

- **No external dependencies**: Everything is self-contained for easy deployment
- **LocalStorage limits**: Browser limit is ~5-10MB; current usage is minimal (<100KB typical)
- **Numerical stability**: Solvers handle rates from -99.99% to 200%, corpus targets up to billions
- **Excel format**: Uses SpreadsheetML (Excel 2003 XML) for maximum compatibility
- **Browser compatibility**: Requires modern browser with ES6+ support (2015+)

## Git Workflow

Standard git commands. No special build or pre-commit steps required.

```bash
git add SIP_Calculator_India.html
git commit -m "description"
git push
```
