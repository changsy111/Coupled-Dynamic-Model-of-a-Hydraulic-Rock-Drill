# Coupled Dynamic Model of a Hydraulic Rock Drill

A MATLAB simulation of the coupled hydraulic and mechanical dynamics of a hydraulic rock drill. The model describes piston impacts, valve motion, accumulator behavior, and nonlinear bit–rock interaction.

## Features

- Coupled hydraulic and mechanical equations with 24 state variables.
- Hydraulic pressure and flow calculations.
- Piston, tool, drill bit, casing, and valve motion.
- Event-based collision handling and accumulator mode switching.
- Nonlinear rock contact with irreversible penetration.
- Fixed-duration simulation and optional steady-state assessment.
- Automatic plotting and data export.

## Requirements

- MATLAB with the `ode15s` solver.
- All supplied `.m` files must be in the same folder or on the MATLAB path.

## Quick Start

Download or clone this repository, open MATLAB, and navigate to the `coupled_drilling` folder.

Run:

```matlab
result = drilling_system_3s_fast;
```

This function simulates the system from 0 to 3 seconds and plots the full interval. It does not require the system to reach steady state.

The simulation may stop early if a physical domain boundary or numerical failure is detected. The 3-second duration refers to simulated time, not execution time.

To display the figures without saving output files:

```matlab
result = drilling_system_3s_fast(struct('SaveResults', false));
```

To also export the complete time-series data as CSV:

```matlab
result = drilling_system_3s_fast(struct('SaveTimeseriesCSV', true));
```

## Steady-State Assessment

To enable termination after confirmed steady-state convergence, use:

```matlab
result = drilling_system_steady(struct( ...
    'StopWhenSteady', true, ...
    'DiscardTime', 0, ...
    'MaxTime', 3));
```

The convergence check compares successive impact cycles using relative states, impact periods, impact energies, and drilling advances. Reaching the time limit does not imply convergence.

Inspect:

```matlab
result.status
result.converged
result.message
result.cycleMetrics
```

## Main Files

| File | Description |
|---|---|
| `drilling_system_3s_fast.m` | Runs a fixed 3-second simulation and plots the results. |
| `drilling_system_steady.m` | Supports fixed-duration simulation and steady-state assessment. |
| `drilling_parameters.m` | Defines physical parameters, numerical scales, and initial conditions. |
| `drilling_rhs.m` | Implements the coupled governing equations. |
| `drilling_events.m` | Defines collision, mode-switching, and physical-domain events. |
| `drilling_invariant.m` | Constructs translation-invariant quantities for convergence assessment. |
| `drilling_derive.m` | Calculates derived physical quantities. |
| `drilling_plot_results.m` | Generates simulation figures. |

## Outputs

The returned `result` structure contains:

- Simulation time and state histories.
- Hydraulic pressures, displacements, and velocities.
- Impact times and cycle-level metrics.
- Relative impact energy and collision energy loss.
- Impact frequency and penetration rate.
- Simulation status and termination information.

By default, the fixed-duration program saves results in `results_3s_fast`, including:

- `drilling_steady_result.mat`: complete simulation results.
- `drilling_cycle_metrics.csv`: impact-cycle metrics.
- `drilling_figure_*.png`: simulation figures.
- `drilling_timeseries.csv`: optional time-series export.

The model uses SI units internally. Hydraulic state pressures are expressed as gauge pressures in pascals; absolute pressure is used where required.

## Parameter Adjustment

Model parameters can be edited in `drilling_parameters.m` or supplied through the options structure:

```matlab
p = drilling_parameters();

% Example: adjust the feed-pressure setting.
p.feedSetPressure = 5e6;

result = drilling_system_3s_fast(struct('Parameters', p));
```

When changing parameters, check that the initial conditions remain physically consistent.

## Performance

The fixed-duration program stores trajectory segments and concatenates them after integration to reduce repeated memory allocation. Time-series CSV export is disabled by default.

Execution time depends on solver tolerances, event frequency, model stiffness, and computer resources. Changes to numerical settings should be checked for their effects on impact peaks and cycle-level results.

## Model Scope and Limitations

This program is intended for research and numerical investigation. It uses assumed geometry and parameter values and is not an experimentally identified model of a specific commercial rock drill.

A completed simulation does not establish physical accuracy or steady-state convergence. Model predictions should be checked against experimental data before quantitative engineering use.
