# Import python packages

import streamlit as st;
import numpy as np;
import pandas as pd;
from decimal import Decimal;

# ---------------- Constants ----------------
M_TO_FT = 3.28084;  # meters -> feet


# ---------------- Helpers ----------------
def build_weights(category: str) -> list[Decimal]:
    if category == "Women":
        # 2.5–7.5 by 0.5 (11 values)
        return [Decimal("2.5") + Decimal("0.5") * i for i in range(11)]
    # Men: 5.0–12.0 by 0.5 (15 values)
    return [Decimal("5.0") + Decimal("0.5") * i for i in range(15)]

def to_meters(value: float, unit: str) -> float:
    return value if unit == "Meters" else value / M_TO_FT

def from_meters(value_m: float, unit: str) -> float:
    return value_m if unit == "Meters" else value_m * M_TO_FT

def highlight_base_row(df: pd.DataFrame, base_weight: float):
    # Highlights the row where Weight (kg) == base_weight
    def _hl(row):
        is_base = abs(float(row["Weight (kg)"]) - base_weight) < 1e-9
        return ["background-color: #fff2cc"] * len(row) if is_base else [""] * len(row)

    return df.style.apply(_hl, axis=1);

st.title('Hammer Throw Distance Conversion Calculator');
st.write('By inputting the distance of one ball, users can see target distances for other weights.');
st.caption("Rule: every +1.0 kg = 6.0 meters less distance (linear conversion).")


top1, top2 = st.columns(2)
with top1:
    category = st.radio("Category", ["Women", "Men"], horizontal=True)

with top2:
    unit = st.radio("Units", ["Meters", "Feet"], horizontal=True)

weights = build_weights(category)

col1, col2 = st.columns(2)
with col1:
    base_weight = st.selectbox(
        "Which implement did you throw?",
        options=weights,
        format_func=lambda w: f"{w} kg"
    )

with col2:
    base_distance_display = st.number_input(
        f"Distance for {base_weight} kg ({'m' if unit == 'Meters' else 'ft'})",
        min_value=0.0,
        value=50.0,
        step=0.5
    )

# ---------------- Compute ----------------
base_weight_f = float(base_weight)
base_distance_m = to_meters(base_distance_display, unit)

rows = []
for w in weights:
    w_f = float(w)
    delta_kg = w_f - base_weight_f

    # Convert in meters (core rule)
    converted_m = base_distance_m - 6.0 * delta_kg

    # Clamp at 0 meters
    converted_m = max(converted_m, 0.0)

    # Convert back to display unit
    converted_display = from_meters(converted_m, unit)

    rows.append({
        "Weight (kg)": w_f,
        f"Converted distance ({'m' if unit == 'Meters' else 'ft'})": round(converted_display, 2)
    })

df = pd.DataFrame(rows)

# ---------------- Table ----------------
st.subheader("Converted targets")
st.dataframe(
    highlight_base_row(df, base_weight_f),
    use_container_width=True,
    hide_index=True
)
