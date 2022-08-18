# Python-guideline

We use [black](https://github.com/psf/black), the "Uncompromising Code Formatter" before submitting pull-requests. It provides a consistent style, which is useful when diffing.

Please use type-hints as much as possible

### Structure of the main classes

The main classes, such as `Parameter`, `NPI`, `SeedingAndInitialConditions`,`Compartments` should tend to the same struture:

* a `writeDF`
* function to plot
* (TODO: detail pipeline internal API)

