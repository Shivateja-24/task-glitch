# TaskGlitch – Bug Fixes Challenge (Erino SDE Intern Assignment)

This repository contains fixes for the **TaskGlitch** Task Management Web App provided as part of the Erino SDE Intern assignment.  
The objective was to identify and resolve multiple bugs affecting **data consistency, UI behavior, sorting stability, and ROI calculations**, and make the application stable and production-ready.

---

## 🐞 Bug 1: Double Fetch Issue (Duplicate Task Loading)

### Problem

On page load, the task-loading logic executed **twice**, resulting in:

- Duplicate task entries
- Inflated metrics (total revenue, ROI, etc.)

### Cause

- Multiple initialization triggers caused the fetch logic to run more than once during app startup.

### Fix

- Guarded the data-loading logic to ensure it executes **only once**.
- Removed the secondary opportunistic fetch that appended duplicate tasks.

### Result

- Tasks are loaded a single time.
- No duplicated data.
- Metrics now reflect correct values.

---

## 🐞 Bug 2: Undo Snackbar State Issue

### Problem

After deleting multiple tasks:

- The snackbar undo action could restore an **older deleted task** instead of the most recent one.

### Cause

- The `lastDeleted` task state was not cleared correctly after the snackbar closed.

### Fix

- Ensured only the **most recently deleted task** is stored.
- Undo action restores only the active deletion.
- Snackbar lifecycle correctly resets deletion state.

### Result

- Undo behaves predictably.
- No phantom or stale task restorations.

---

## 🐞 Bug 3: Unstable Sorting (ROI Tie Flickering)

### Problem

When tasks had:

- Same ROI
- Same priority  
  their order changed on every render, causing a flickering UI.

### Cause

- The sorting logic used a **random tie-breaker** (`Math.random()`), making the sort non-deterministic.

### Fix

- Removed the random tie-breaker.
- Added a **deterministic secondary comparison** (stable ordering).

### Result

- Sorting is stable and consistent.
- Tasks no longer reshuffle on re-render.

---

## 🐞 Bug 4: Multiple Dialogs Opening (Event Bubbling)

### Problem

Clicking **Edit** or **Delete** also triggered the row click handler, causing:

- Edit/Delete dialog
- View dialog  
  to open simultaneously.

### Cause

- Event bubbling from nested click handlers inside a clickable table row.

### Fix

- Added `e.stopPropagation()` to Edit and Delete button click handlers.

### Result

- Clicking:
  - Row → opens View dialog only
  - Edit → opens Edit dialog only
  - Delete → opens Delete confirmation only

---

## 🐞 Bug 5: ROI Calculation Errors (NaN / Infinity)

### Problem

ROI calculations produced invalid values when:

- `timeTaken = 0`
- Revenue or time values were invalid  
  leading to `NaN` or `Infinity` in UI and metrics.

### Cause

- Unsafe division without validation.

### Fix

- Added defensive checks before calculating ROI:
  - Prevent division by zero
  - Validate numeric inputs
  - Return safe fallback values
- Formatted ROI to two decimal places.

```ts
export function computeROI(revenue: number, timeTaken: number): number {
  if (
    typeof revenue !== "number" ||
    typeof timeTaken !== "number" ||
    timeTaken <= 0
  ) {
    return 0;
  }
  const roi = revenue / timeTaken;
  return Number.isFinite(roi) ? Number(roi.toFixed(2)) : 0;
}
```

---

## ✅ Final Outcome

- All 5 reported bugs fixed
- Application is stable and predictable
- Sorting and metrics behave correctly
- UI interactions work as intended
- Edge cases handled safely

---

## 🚀 Deployment

The application is deployed and publicly accessible as required.

**Live URL:**

---
