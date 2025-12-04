# CCW Build and Verification Notes

The structured text program cannot be compiled in this environment because Connected Components Workbench (CCW) is not available. Use the steps below in CCW to build and confirm the outputs.

## Build steps in CCW
1. Open the project and select the Micro870 controller.
2. Open **Project Organizer → Programs → PROG1** and paste the updated structured text from `ST FILE`.
3. From the toolbar, choose **Verify Project** (or **Logic → Verify**). Confirm that no syntax errors are reported.
4. Download to the controller (or the emulator) with **Communications → Download** if you want to run the logic.

## Run-time checks
1. Go online, then switch to **Run** mode.
2. Watch the following tags in the variable monitor:
   - `_IO_EM_DI_00`, `_IO_EM_DI_01` (pushbuttons)
   - `PB2_Num` (DINT from `Counter3.CV`; should increment with qualified green button presses)
   - `FactorialResult` and `FactorialTime` (now set via CASE lookup; remain 0 unless `PB2_Num` is 1–7)
   - `TON_2.Q` and `_IO_EM_DO_00` (green stack light output)
3. Test a few cases (lookup-driven, no FOR loop required):
   - Single valid press (`PB2_Num = 1`): `FactorialTime = 1 ms`.
   - Three valid presses (`PB2_Num = 3`): `FactorialTime = 6 ms`.
   - Five valid presses (`PB2_Num = 5`): `FactorialTime = 120 ms`.
   - Seven valid presses (`PB2_Num = 7`): `FactorialTime = 5040 ms`.
   - Eight or more presses: `FactorialTime` should remain `0 ms` to avoid overflow.
4. Press and hold the red pushbutton (`_IO_EM_DI_01`): `TON_2` should hold `_IO_EM_DO_00` TRUE for `FactorialTime`. When the timer finishes, `Final_Reset` should pulse to clear the counters.

## Troubleshooting tips
- If you see a type error on `PB2_Num`, confirm it is declared as `DINT` (to match `Counter3.CV`) and that the assignment `PB2_Num := Counter3.CV;` is used without extra conversions.
- Ensure both timers `TON_1` and `TON_2` are declared as `TON` function block instances.
- Keep `PB2_Num` within 1–7; higher values default the time to 0 ms in this lookup-based program.
- The factorial values are precomputed, so no `FOR` loop types need to match—this avoids prior INT/DINT loop bound errors.
