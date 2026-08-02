YAAW: Yet Another Argyll Wrapper - or Workflow

A transparent, cross-platform printer-profiling workflow for ArgyllCMS

YAAW is a deliberately lightweight printer-profiling front end for ArgyllCMS, the colour-management
software written by Graeme Gill. It began life as a small shell-script menu of preset commands and has
grown into a portable Python/tkinter application while retaining the same KISS philosophy: expose
ArgyllCMS rather than hide it, keep every generated command visible, and make the normal profiling
workflow repeatable without turning it into a black box.
YAAW has been developed and tested primarily under Linux Mint and macOS. Other Unix-like systems
should work in principle, although paths, default applications, and platform-specific command behaviour
may require adjustment.

Design philosophy
• Pure Python and tkinter: no GUI framework or browser-engine dependency is required.
• Transparent operation: the ArgyllCMS command lines remain visible, editable, and logged.
• Conservative automation: YAAW assists with the workflow but does not attempt to second-guess
ArgyllCMS.
• Portable project data: each job retains its targets, measurements, profile, logs, and JSON
configuration.
• KISS first: useful diagnostics are available, but the primary workflow remains four clear steps.

The profiling workflow
YAAW organises the normal printer-profiling process into four stages: Create Target, Print Target,
Read Target, and Build Profile.

1. Configuration
The Configuration page provides editable presets for the commonly used options of targen, printtarg,
chartread, and colprof. Each tool also has an Additional Arguments field, allowing less-common
ArgyllCMS options to be added without changing YAAW itself. Built-in help buttons open the installed
ArgyllCMS HTML documentation in YAAW's native viewer.
Printer, paper, ink, instrument, paper size, and patch count are used to generate a readable project
identity. The working directory records the printer/paper/ink combination, while generated filenames and
internal descriptions include the instrument and patch count. This permits several profile runs for the
same paper to coexist without becoming anonymous.
Project configurations are saved as JSON files and can be reloaded later. A similar job can therefore be
used as a template: load it, change the paper or another identifying field, and continue with the new run.

2. Target generation and printing
Instrument-specific patch-layout tables provide practical paper-size and patch-count choices. The
ColorMunki layouts are author-tested; i1 and i1Pro3+ entries are based on printtarg output and may
require confirmation with the physical instrument. Instrument, patch count, and paper size remain
manually editable, so custom combinations are not blocked.
YAAW supports multi-page targets and deliberately allows larger nominal targets to be printed at a
reduced physical size. A3 targets may be condensed onto A4, and rotated A2R targets may be printed
onto A3. This permits substantially higher patch densities while retaining reliable strip reading when a
suitable guide is used.
For the ColorMunki, proven examples include 460 patches on one A4 sheet and 960 patches on one
A3 sheet. Standard lower-density Argyll layouts remain available, and further condensation may be
explored with printtarg's -a option through Additional Arguments.

3. Measurement
chartread runs in a lightweight terminal-style window connected to a pseudo-terminal on Linux and
macOS. Calibration prompts, strip-reading progress, warnings, and instrument messages remain visible
while the relevant keyboard input is passed directly to chartread.
Before measurement begins, YAAW asks ArgyllCMS to enumerate connected instruments and checks
that the selected instrument family is present. This catches missing or mismatched hardware before
overwrite prompts or target reading begin.

4. Profile creation and validation
colprof builds the ICC profile using the selected quality, intent, metadata, rendering-profile, and
additional-argument settings. The resulting working profile is retained with the project files and copied to
the configured ICC output directory, which defaults to:

~/.local/share/color/icc

profcheck then performs a compact validation and records a more detailed sorted report in the
persistent project logfile. The report includes the highest delta-E patches and can generate a rotatable
3D profile-error map showing measured-versus-predicted Lab residuals.
Inspection and diagnostics

Once Step 4 has completed successfully, the Execution page enables a Details view. This replaces the
live-output display with the persistent project logfile and exposes the inspection tools:

• Show Gamut — opens the static gamut viewer with a*b*, L*a*, and L*b* views plus profile metrics.
• 3D Error Map — opens profcheck's rotatable X3DOM model of profile-fit errors in the system
browser.
• 3D Gamut — available within the static gamut popup and opens iccgamut's rotatable X3DOM gamut
model.

For older completed projects, YAAW can generate a missing profcheck 3D Error Map on demand from
the existing TI3 measurement file and working ICC profile; the profile itself is not rebuilt.
The static and 3D gamut views are independent of the profcheck error map. The gamut displays show
the profile's colour-volume shape and extent, while the error map shows where profile predictions differ
from the measured patches.

Logging, persistence, and safety
• A persistent logfile records commands, diagnostics, warnings, and profile-validation results.
• The live Execution output is maintained separately from the persistent logfile.
• Each profiling job stores a complete JSON configuration beside its Argyll files.
• Crash recovery preserves the active session and can resume from the last meaningful stage.
• Overwrite checks protect existing targets, measurements, profiles, and related files.
• Starting a new workflow step invalidates stale Details state until a new Step 4 succeeds.
• Previously installed ICC profiles are not removed merely because a later rebuild fails.

Typical staged-printing workflow
The saved project configuration also supports a practical split workflow. Several targets can be
generated and printed in one session, then left to dry. The corresponding JSON jobs can later be
reopened for chartread measurement and profile generation without recreating the targets or re-
entering the settings.

Requirements
• Python 3 with tkinter.
• ArgyllCMS tools: targen, printtarg, chartread, colprof, profcheck, and iccgamut.
• A strip-reading spectrophotometer supported by ArgyllCMS, typically ColorMunki/i1Studio, i1Pro, or
i1Pro3+.
• A normal desktop environment with an image viewer and web browser for target and X3DOM
display.
• A suitable measurement guide when using condensed strip-reading targets.
Installation and paths

YAAW is distributed as a single Python script. ArgyllCMS may be installed from the operating system's
package manager or unpacked from the official upstream archive. At startup YAAW searches the
current PATH and several common manual-install locations, including versioned Argyll directories
under the user's home directory, /opt, and /usr/local.
Default working, profile-output, browse, and crash-recovery locations are defined near the top of the
script and may be edited where local conventions differ.

Current feature summary
• Automatic field population and readable project naming
• Editable instrument, patch-count, and paper-size controls
• Instrument-specific empirical layout guidance
• ICC preconditioning and rendering-profile support
• Optional arguments for every principal Argyll profiling command
• Multi-page and condensed target workflows
• Connected-instrument preflight before chartread
• Pseudo-terminal chartread interaction on Linux and macOS
• Persistent logs, saved JSON configurations, and crash recovery
• Profile validation with compact and detailed profcheck output
• Static gamut plots, profile metrics, 3D gamut, and 3D profile-error map
• Portable use of the system browser for X3DOM visualisations

Scope
YAAW is not intended to hide ArgyllCMS, conceal its terminology, or impose a single profiling recipe.
It is a structured and repeatable way to run the standard Argyll tools, with sensible defaults, visible
commands, editable arguments, and enough project management to make repeated printer profiling
less error-prone.

In short: YAAW does not claim to make colour management simple. It aims to make a powerful,
well-established command-line workflow easier to operate, inspect, repeat, and understand.
