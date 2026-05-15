## Phase 2 Analysis Implementation Guide

This document explains how to use the two new analysis files to complete Phase 2 requirements.

---

## 📁 Files Created

### 1. `Phase2_Complete_Analysis.ipynb` (Recommended - Start Here)
A comprehensive Jupyter notebook with all three Phase 2 components:
- **Section 1**: Load and prepare data
- **Section 2**: Adaptation & Recovery Latency (Measurements 1 & 2)
- **Section 3**: Mode Sensitivity analysis (Screen Share vs Video)
- **Section 4**: Correlation Matrix (Pearson r values)
- **Section 5**: Save results to CSV and PNG

**Advantages:**
- Run cells individually to explore each component
- See visualizations immediately
- Easy to debug and modify parameters
- Generate publication-ready figures

### 2. `analysis_phase2_complete.py`
A standalone Python script with all functions (useful for batch processing or automation).

---

## 🚀 Quick Start

### Option A: Using Jupyter Notebook (Recommended)

1. **Open the notebook:**
   ```
   Phase2_Complete_Analysis.ipynb
   ```

2. **Update data paths** (Section 3 - Mode Sensitivity):
   ```python
   scenario_configs = {
       'Video (Default)': 'run_default/detailed_qoe_results.csv',
       'Screen Share': 'run_wifi_screen_share/stats.csv',  # Update this path
   }
   ```

3. **Run all cells in order** (Kernel → Run All)

4. **Review outputs:**
   - Console output with statistics
   - PNG figures saved to current directory
   - CSV files saved to `phase2_results/` folder

### Option B: Using Python Script

```bash
cd c:\Users\aszew\OneDrive - University of Illinois - Urbana\Desktop\zoom-analysis-Szewczyk

python analysis_phase2_complete.py
```

---

## 📊 What Each Section Measures

### Section 2: Adaptation & Recovery Latency

**Reaction Time (Loss → FPS Drop)**
```
Formula: t_reaction = t_fps_drop - t_loss_start

Where:
- t_loss_start: When packet loss > 5% begins
- t_fps_drop: When FPS drops 50% or below 15 FPS
- Result: Mean, Median, P95 values in seconds
```

**Recovery Time (Loss Stop → QoE >0.9)**
```
Formula: t_recovery = t_qoe_recovery - t_loss_end

Where:
- t_loss_end: When packet loss drops back below 5%
- t_qoe_recovery: When QoE score returns to ≥0.9
- Result: Mean, Median, P95 values in seconds
```

### Section 3: Mode Sensitivity

**Brittleness Metric:**
```
Brittleness = (QoE_no_loss - QoE_high_loss) / QoE_no_loss × 100%

Where:
- High_loss: Loss rate > 10%
- Higher % = More brittle = Breaks faster under WiFi interference
```

**Hypothesis Testing:**
- **Screen Share** → Lower FPS + Higher Bitrate = Sharpness Priority
- **Video** → Higher FPS + Lower Bitrate = Smoothness Priority

### Section 4: Correlation Analysis

**Pearson Correlation Coefficient (r):**
```
r ∈ [-1, +1]

Strength interpretation:
- |r| > 0.8 = VERY STRONG predictor
- |r| > 0.6 = STRONG predictor  
- |r| > 0.4 = MODERATE predictor
- |r| < 0.4 = WEAK predictor
```

---

## 📈 Output Files

### Generated CSV Files (saved to `phase2_results/`)

1. **reaction_latencies.csv**
   - Columns: event_start, fps_drop_ts, reaction_latency_sec, baseline_fps, observed_fps
   - Use: Show how fast Zoom adapts to network problems

2. **recovery_latencies.csv**
   - Columns: event_end, qoe_recovery_ts, recovery_latency_sec, recovered_qoe
   - Use: Show how long users suffer after network recovers

3. **correlation_matrix.csv**
   - Shows Pearson r values between all metric pairs
   - Use: Identify strongest QoE predictor

4. **mode_sensitivity_summary.csv**
   - Comparison table: Video vs Screen Share modes
   - Use: Show which mode is more brittle

### Generated PNG Figures

1. **phase2_adaptation_latency.png**
   - Histograms of reaction and recovery latencies
   - Use: Section 3 of paper (Results)

2. **phase2_correlation_heatmap.png**
   - Heatmap showing all correlations
   - Use: Section 4 of paper (Discussion)

3. **phase2_metric_vs_qoe_scatter.png**
   - 4 scatter plots (loss/jitter/fps/bitrate vs QoE)
   - Use: Paper visualization showing which metric matters most

---

## 🔧 Customization Options

### Adjust Loss Event Detection Threshold
```python
loss_events = detect_loss_events(df, 
                                loss_threshold=0.05,    # Change: 5% loss
                                min_duration=2)          # Change: 2 seconds minimum
```

### Adjust Recovery Target QoE
In function `calculate_recovery_latency()`:
```python
qoe_recovery_target = 0.9  # Change: aim for 0.8 or 0.95 instead
```

### Adjust FPS Drop Detection
In function `calculate_reaction_latency()`:
```python
fps_threshold = 15  # Change: any FPS below this = "dropped"
```

---

## ✅ Validation Checklist

- [ ] `Phase2_Complete_Analysis.ipynb` runs without errors
- [ ] All 3 sections produce output
- [ ] Reaction latencies show reasonable times (0-5 seconds typically)
- [ ] Recovery latencies show reasonable times (0-10 seconds typically)
- [ ] Mode comparison shows both scenarios (if available)
- [ ] Correlation matrix shows 4x4 grid of values
- [ ] CSV files created in `phase2_results/`
- [ ] PNG figures created

---

## 📋 For Your Paper

### Figure References

1. **Figure 1 (Intro)**: Workflow diagram
   - [ ] Create manually or use graphviz

2. **Figure 2 (Methodology)**: QoE formula
   - [ ] Copy from existing papers or create LaTeX

3. **Figure 3 (Results)**: Adaptation Latency + QoE Scatter
   - [ ] Use `phase2_adaptation_latency.png`
   - [ ] Use `phase2_metric_vs_qoe_scatter.png`

4. **Table 1 (Results)**: Mode Comparison
   - [ ] Use `mode_sensitivity_summary.csv` 
   - [ ] Format as LaTeX table

5. **Table 2 (Discussion)**: Correlation Matrix
   - [ ] Use `correlation_matrix.csv`
   - [ ] Format as LaTeX table

6. **Figure 4 (Discussion)**: Heatmap
   - [ ] Use `phase2_correlation_heatmap.png`

---

## ⚠️ Troubleshooting

### "No loss events detected"
- Adjust `loss_threshold` parameter (try 0.02 or 0.10)
- Check if your data actually has packet loss

### "CSV file not found"
- Verify file paths in `scenario_configs`
- Use absolute paths if relative paths fail
- Check that CSV files exist

### "Insufficient columns"
- Verify `detailed_qoe_results.csv` has calculated QoE already
- If using raw `stats.csv`, script calculates it automatically

### Different results between runs?
- Normal - no randomness, but depends on input data
- If using filtered data, results may vary

---

## 📚 References

**QoE Formula** (from your existing implementation):
```
QoE = 0.4 × (1 - loss_rate) + 0.3 × fps_norm + 0.3 × jitter_norm

Where:
- fps_norm = min(frames / 30, 1.0)
- jitter_norm = max(1 - jitter_ms / 40, 0)
```

**Pearson Correlation**:
r = Σ((x - x̄)(y - ȳ)) / √(Σ(x - x̄)² × Σ(y - ȳ)²)

---

## 🆘 Need Help?

1. Check console output for error messages
2. Verify all required columns exist in your CSV files
3. Run `df.columns` to see available columns
4. Check data types: `df.dtypes`

Good luck with your paper! 🚀
