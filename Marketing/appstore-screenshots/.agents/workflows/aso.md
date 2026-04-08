---
description: Generate ASO-optimized App Store screenshots
---

This workflow guides you through creating high-converting App Store screenshots.

1. **Benefit Discovery**: Analyze the codebase to find 3-5 core user benefits.
2. **Screenshot Pairing**: Upload or provide paths to simulator screenshots. Pair them with benefits.
3. **Draft Design**: Pick a brand colour (hex).
4. **Generation**: 
   - Run `python3 compose.py` to create the layout scaffold.
   - Use `generate_image` with the scaffold as base (`ImagePaths`) to enhance and polish.
   - Run the crop/resize script (via `sips`) to hit exact App Store dimensions.
5. **Finalize**: Approve the best versions and generate a showcase.

**Commands**:
- `/aso benefits` -> Start discovery
- `/aso screenshots` -> Provide screenshots
- `/aso generate` -> Start generating

> [!NOTE]
> All progress is saved in `./.aso_memory/` so you can resume at any time.
