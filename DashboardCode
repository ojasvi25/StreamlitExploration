import streamlit as st
import pandas as pd
import plotly.express as px

# Load the dataset
df = pd.read_excel('entire_performance_summary.xlsx', sheet_name='Sheet1')

st.set_page_config(page_title="Model Performance Dashboard", layout="wide")

st.title("📊 Model Performance Metrics Dashboard")

# Sidebar filters
st.sidebar.header("Filter Options")
model_options = ["All"] + sorted(df['Model'].unique())
balance_options = ["All"] + sorted(df['Data Balancing'].unique())

selected_model = st.sidebar.selectbox("Select Model", model_options)
selected_balance = st.sidebar.selectbox("Select Data Balancing", balance_options)

# Apply filters
filtered_df = df.copy()

if selected_model != "All":
    filtered_df = filtered_df[filtered_df['Model'] == selected_model]

if selected_balance != "All":
    filtered_df = filtered_df[filtered_df['Data Balancing'] == selected_balance]

# --- KPI Metrics Section ---
st.subheader("📌 Key Performance Indicators (KPIs)")

if filtered_df.empty:
    st.warning("No data available for selected filters.")
else:
    if selected_model != "All" and selected_balance != "All":
        row = filtered_df.iloc[0]

        col1, col2, col3, col4, col5, col6 = st.columns(6)
        col1.metric("Accuracy", f"{row['Accuracy']:.3f}")
        col2.metric("Precision", f"{row['Precision (Class 1)']:.3f}")
        col3.metric("Recall", f"{row['Recall (Class 1)']:.3f}")
        col4.metric("F1-Score", f"{row['F1-Score (Class 1)']:.3f}")
        col5.metric("F2-Score", f"{row['F2-Score (Class 1)']:.3f}")
        col6.metric("MCC", f"{row['MCC']:.3f}")

        # --- Confusion Matrix Section ---
        st.subheader("📌 Confusion Matrix")

        cm_data = {
            "Predicted Negative": [row["True Negatives"], row["False Negatives"]],
            "Predicted Positive": [row["False Positives"], row["True Positives"]],
        }
        cm_df = pd.DataFrame(cm_data, index=["Actual Negative", "Actual Positive"])

        st.table(cm_df)

    else:
        st.info("Displaying aggregated view since multiple models / balancing types are selected.")

        # --- Aggregated Bar Chart for Comparison ---
        kpi_columns = ['Accuracy', 'Precision (Class 1)', 'Recall (Class 1)', 'F1-Score (Class 1)', 'F2-Score (Class 1)', 'MCC']

        melted_df = filtered_df.melt(
            id_vars=['Model', 'Data Balancing'],
            value_vars=kpi_columns,
            var_name='Metric',
            value_name='Value'
        )

        fig = px.bar(
            melted_df,
            x='Model',
            y='Value',
            color='Metric',
            barmode='group',
            facet_col='Data Balancing',
            title='KPI Comparison Across Models'
        )

        st.plotly_chart(fig, use_container_width=True)
