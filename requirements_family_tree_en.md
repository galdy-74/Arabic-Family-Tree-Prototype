# Software Requirements Specification (SRS)

**Project:** Professional Family Tree Application — Phase 1 (Single HTML Prototype)
**Version:** 1.0
**Status:** Initial UI prototyping phase only — no business logic
**Document Type:** Functional & Technical Requirements

> **Note:** This document is written in English for engineering/reference purposes.
> The application's user interface itself remains fully in Arabic, with RTL
> (right-to-left) layout, as specified in Section 5.

---

## 1. Project Overview

### 1.1 Objective

Build an initial prototype (Prototype) of the main interface for a professional
Family Tree management application, delivered as a **single HTML file**,
structurally and technically ready to be:

- Connected later to a local **Realm Database (RealmDB)**.
- Packaged later as a desktop application using the **Electron** framework.

### 1.2 Scope of This Phase

- Design of the **Main Window / UI only**.
- **No** actual database connection.
- **No** real business logic — mock/placeholder data only, for display purposes.
- The resulting file must run by opening it directly in a browser, with no
  server or build tools required.

### 1.3 Out of Scope (for now)

- Actual database integration.
- Save, search, edit, and delete logic.
- Packaging the app inside Electron (deferred to a later phase, but the design
  must remain compatible with it).

---

## 2. Technical Stack

| Component              | Technology                                                                                     |
| ---------------------- | ---------------------------------------------------------------------------------------------- |
| Structure              | A single `index.html` file containing HTML + CSS + JavaScript (Vanilla JS)                     |
| Styling                | CSS inside a single `<style>` tag, organized with CSS Variables for easy future extraction     |
| Behavior               | JavaScript inside a single `<script>` tag, split into logical modules using an IIFE or internal classes/objects |
| Target future database | Realm Database (local)                                                                         |
| Target future runtime  | Electron (desktop application)                                                                 |
| Encoding               | Full UTF-8                                                                                     |
| Application language   | Fully Arabic UI text, RTL layout                                                               |

### 2.1 Why a single file at this stage?

To allow fast visual review and design validation before moving to the final
multi-file structure required by Electron, while still fully respecting internal
logical separation even though everything currently lives in one file.

---

## 3. Future Electron Compatibility Requirements

The file must be built so it can later be split into the standard Electron
application structure **without rewriting the logic**:

- Do not rely on browser-only APIs as a final storage solution (e.g.,
  `localStorage`) — it may only be used for temporary mock data, with a clear
  code comment indicating it will be replaced by Realm.
- Split the JavaScript into clear logical modules within the same file
  (e.g., `UIController`, `DataService`, `RendererState`) so each module can
  later be moved into its own file (`main.js`, `renderer.js`, `preload.js`)
  when migrating to Electron.
- Do not use absolute file paths tied to a specific operating system.
- Prepare a clear entry point (`function initApp()`) called on page load, which
  will serve as the same initialization point when migrating to Electron.
- Do not use any external library that requires build tools (bundlers) at this
  stage; any external library (if used) must be loaded via CDN within the same file.

---

## 4. Future Realm Database Compatibility Requirements

Since actual database integration is deferred, the following must be respected
to prepare the groundwork without implementing it:

### 4.1 Data Access Layer

Create a mock module/object with a clear name such as `DataService` that mimics
the functions Realm will provide later:

- `getAllPersons()`
- `getPersonById(id)`
- `addPerson(personObject)`
- `updatePerson(id, personObject)`
- `deletePerson(id)`
- `getAllFamilies()`

At this stage, these functions must return fixed mock data or empty arrays, with
a comment `// TODO: replace with actual Realm logic later` above each function.

### 4.2 Data Schema Draft

Prepare an initial draft of the object structure (Schema) consistent with how
Realm defines schemas (object name + fields + types), **without activating it**,
to serve as a reference for the next phase. Example fields required for a
"Person" object:

- id
- Full name
- Gender
- Father ID / Mother ID (reference relationships)
- Spouse ID
- List of children IDs
- Date of birth / Place of birth
- Date of death
- Occupation, phone, email, address, notes
- Profile photo path

### 4.3 Constraints for This Phase

- No actual read/write operation is allowed.
- All data displayed in the UI must be sample/mock data, embedded in the code in
  a clearly separated and organized way (a dedicated variable or structured mock
  data block), so it can be removed in one step later.

---

## 5. UI Requirements (based on the approved design)

> **Important:** All requirements below apply to the visual design and layout
> structure. All user-facing text remains in Arabic, and the entire page
> direction is RTL. Only internal element names/IDs and code comments in this
> document are written in English for developer clarity.

### 5.1 Identity and Title

- Page/window title (displayed in Arabic): **"برنامج شجرة العائلة الاحترافي"**
  (Professional Family Tree Program).
- An appropriate family-tree-themed icon (temporary Favicon at this stage).
- Full page direction: **RTL**, with all visible text in Arabic.

### 5.2 Typography

- Primary font: **Cairo** (loaded via Google Fonts CDN).
- Fallback fonts on failure: Noto Kufi Arabic, then Segoe UI.
- Base font size: 11–12px/pt depending on the element.

### 5.3 Responsive Layout

- Use CSS Flexbox / CSS Grid only to structure the layout — avoid absolute
  positioning (`position: absolute`) except for strictly necessary cases (such
  as notification/toast layers).
- Default target window size once running as an Electron app later: **1600×900**,
  minimum size: **1366×768** — this must be reflected in the CSS (e.g.,
  `min-width` on the main container).
- Elements must automatically reflow/rearrange on resize.

### 5.4 Overall Interface Structure

- **Top menu bar** (Arabic labels): File, Edit, View, People, Family, Reports,
  Settings, Help — with the same sub-items defined in the original design document.
- **Toolbar** directly below the menu bar, with large buttons (icon + Arabic
  text) and tooltips, for key actions (New Project, Open, Save, Print, Add
  Person, Edit, Delete, Add Son, Add Spouse, Search, Reports).
- **Three-panel main split** (emulating QSplitter behavior via resizable CSS
  Grid/Flexbox):
  - **Right panel (~280px):** search box, category list, families tree, person
    list, vertical scrolling.
  - **Center panel (largest area):** tree drawing area (Canvas/SVG placeholder),
    a secondary toolbar on top (Zoom In, Zoom Out, 100%, Recenter, Auto Layout,
    Fullscreen), and a default message **"لا توجد بيانات لعرضها"** ("No data to
    display") shown when empty.
  - **Left panel (~320px):** full person info card (photo, full name, gender,
    father, mother, spouse, children, date/place of birth, date of death,
    occupation, phone, email, address, notes) with action buttons (Edit, Delete,
    Add Son, Add Spouse, View Details).
- **Bottom status bar:** number of people, number of families, last saved time,
  database status, application version.

### 5.5 Color System

- Primary color: dark blue (`#0D3B66` or similar).
- Background: white.
- Primary buttons: blue.
- Save button: green.
- Delete button: red.
- Prepare color variables via CSS Variables (`:root`) to make enabling Dark Mode
  later possible without restructuring the CSS.

---

## 6. Code Quality Standards

- Object-oriented / modular JavaScript: use Classes or module-pattern objects
  instead of scattered functions.
- Name all elements with clear English `id` / `class` identifiers, matching as
  closely as possible the previously approved element names (e.g., `mainWindow`,
  `menuFile`, `toolbarMain`, `splitterMain`, `treeViewFamilies`,
  `graphicsViewTree`, `groupPersonInfo`, `btnAddPerson`, `btnEditPerson`,
  `btnDeletePerson`, `btnSearch`, `statusBar`).
- All user-facing text must be in Arabic only.
- Clear code comments explaining each main section of HTML/CSS/JS.
- An explicit comment above every "mock" part indicating it will be replaced
  later (data, database connection, actual save logic, etc.).
- No actual save/search/database connection logic should be written at this stage.
- The code must run by opening the file directly (`file://`) with no console errors.

---

## 7. Upcoming Phases Roadmap (for documentation only — not to be implemented now)

| Phase            | Description                                                                    |
| ---------------- | ------------------------------------------------------------------------------ |
| Phase 1 (current)| Single HTML file — UI only, mock data                                          |
| Phase 2          | Split the file into a standard Electron structure (`main.js`, `preload.js`, `renderer/`) |
| Phase 3          | Integrate actual Realm Database via `DataService`                              |
| Phase 4          | Activate full business logic (add/edit/delete/search/reports)                  |
| Phase 5          | Enable Dark Mode, exports (PDF/Excel), backup/restore                          |

---

## 8. Acceptance Criteria for This Phase

- [ ] A single HTML file that opens directly in any modern browser with no errors.
- [ ] The UI displays all elements described in Section 5, matching the approved design.
- [ ] RTL direction applied across the entire page.
- [ ] The layout is responsive and reflows elements when the browser window is resized/maximized/minimized.
- [ ] A mock `DataService` module exists and is ready to be replaced by Realm.
- [ ] No actual database connection or real save operation exists.
- [ ] The code is organized, commented, and logically separated despite being in a single file.

---

**Closing note:** This document defines the requirements for Phase 1 only (UI
design as a single HTML file) and does not include any actual implementation. It
should be used as the reference to begin implementation in a separate, subsequent
request.