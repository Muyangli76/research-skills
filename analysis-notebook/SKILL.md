---
name: analysis-notebook
description: Create or revise flexible scientific Python notebooks and companion analysis scripts, using a consistent publication plotting style and optional PNG/EPS export. Use for saved-run comparisons, diagnostic analysis, and presentation or journal figures; do not use to change controller, model, or simulation behavior.
---

# Analysis notebook

Create a notebook or companion Python script around the user's scientific
purpose. Inspect the relevant data, existing analysis modules, and nearby
notebooks before deciding the sections and plots. Reuse project loaders,
metrics, and plotting helpers when they already express the intended analysis.

Keep generated work reproducible:

- Use paths relative to a discovered project or repository root; do not embed machine-specific absolute paths.
- Treat saved simulation results as read-only unless the user explicitly requests otherwise.
- Put a short Markdown explanation immediately before each table or figure.
- Keep setup, data loading and validation, analysis, and presentation in distinct cells.
- Generate only the sections needed for the stated purpose. A diagnostic notebook may be detailed; a presentation notebook should be focused.
- When useful, create a companion `.py` script, but do not require one for every notebook.

## Plot standard

Apply [assets/journal_style.mplstyle](assets/journal_style.mplstyle) with
`plt.style.use(...)`. Locate the asset through the installed skill directory or
copy it into an appropriate project style directory when the generated notebook
must run without the skill installed. User instructions for a particular figure
override these defaults.

- Use 15-point text, a white background, no grid, thick data lines, and a clearly visible plot box.
- Do not add an axes title or figure `suptitle` unless the user requests one. Explain the figure in notebook Markdown and use informative axis labels and legends.
- Give every compared series both a distinct color and a distinct line style. Prefer the colorblind-friendly sequence `#0072B2`, `#D55E00`, `#009E73`, `#CC79A7`, `#E69F00`, `#56B4E9`, `#882255`, `#44AA99` and cycle through `-`, `--`, `-.`, and `:`. Add distinct markers when color and line style are insufficient.
- Use zero-order-held stairs for applied actuator commands when the saved values are interval decisions.
- Keep units in axis labels and keep legends frameless unless a frame materially improves readability.
- Avoid relying on transparency because EPS output does not represent it reliably.

## Optional export

Every generated notebook or plotting script should offer PNG and EPS export,
disabled by default. Adapt the export directory and stem to the analysis:

```python
EXPORT_FIGURES = False
EXPORT_FORMATS = ("png", "eps")
EXPORT_DIR = PROJECT_ROOT / "analysis" / "figures" / "chosen_name"


def show_figure(fig, stem):
    if EXPORT_FIGURES:
        EXPORT_DIR.mkdir(parents=True, exist_ok=True)
        for extension in EXPORT_FORMATS:
            kwargs = {"dpi": 300} if extension == "png" else {}
            fig.savefig(
                EXPORT_DIR / f"{stem}.{extension}",
                bbox_inches="tight",
                **kwargs,
            )
    display(fig)
    plt.close(fig)
```

For a plain Python script, replace `display(fig)` with the presentation method
appropriate to the request. Do not enable export merely by generating or
executing the notebook.

## Verification

Check that the notebook is valid JSON, imports resolve from its intended working
directory, export remains off, and representative figures use the style. When
execution is safe and dependencies are available, execute the notebook to catch
cell-order and plotting errors. Do not rerun expensive simulations merely to
validate an analysis notebook.
