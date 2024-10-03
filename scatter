import streamlit as st
import pandas as pd
import plotly.express as px

# Set the page layout to wide
st.set_page_config(layout="wide", page_title="Interactive Scatter Plot", page_icon=":bar_chart:")

# Title with emoji
st.title('✨ Cross-Filterable Scatter Plot from CSV or Excel File')

# Instructions in an expander
with st.expander("ℹ️ Instructions"):
    st.write("""
        Upload a CSV or Excel file containing your data.
        Select the X and Y axes, and optionally choose other attributes such as color and size to customize your scatter plot.
        You can also filter data points using the lasso or box selection tools directly on the plot.
    """)

# Create a sidebar for upload and settings
with st.sidebar:
    st.header("🔧 Settings")

    # Upload file
    uploaded_file = st.file_uploader("Upload a CSV or Excel file", type=["csv", "xlsx"])

    # Add a progress bar while loading the file
    if uploaded_file is not None:
        with st.spinner("Processing file..."):
            if uploaded_file.name.endswith('.csv'):
                df = pd.read_csv(uploaded_file)
            elif uploaded_file.name.endswith('.xlsx'):
                df = pd.read_excel(uploaded_file)
            else:
                st.error('Unsupported file type. Please upload a CSV or Excel file.')
                st.stop()
    else:
        st.info('Please upload a CSV or Excel file to get started.')

# If a file is uploaded, proceed with plotting
if uploaded_file is not None:
    st.success('File loaded successfully!')

    # Preview the entire data in an expandable section
    with st.expander("📊 Data Preview", expanded=False):
        st.dataframe(df)  # Display the entire dataframe

    # Ensure at least two columns exist
    if len(df.columns) < 2:
        st.error('The uploaded file must have at least two columns.')
        st.stop()

    # Use a layout with columns for axis selection
    col1, col2, col3 = st.columns([1, 1, 1])

    with col1:
        x_axis = st.selectbox('Select X-axis', options=df.columns)
    with col2:
        y_axis = st.selectbox('Select Y-axis', options=df.columns, index=1)
    with col3:
        color_option = st.selectbox('Select Color', options=[None] + df.columns.tolist(), index=0)

    # Advanced customization in sidebar
    with st.sidebar:
        st.subheader("🎨 Advanced Plot Customization")
        size_option = st.selectbox('Select Size', options=[None] + df.columns.tolist(), index=0)
        hover_data_options = st.multiselect('Select Hover Data', options=df.columns)

    # Create scatter plot with Plotly
    fig = px.scatter(
        df,
        x=x_axis,
        y=y_axis,
        color=color_option if color_option else None,
        size=size_option if size_option else None,
        hover_data=hover_data_options,
        title=f'Scatter Plot of {y_axis} vs {x_axis}',
        template='plotly_dark',  # Set Plotly theme
        labels={x_axis: f"{x_axis} (units)", y_axis: f"{y_axis} (units)"}  # Optional axis labels
    )

    # Improve plot layout
    fig.update_layout(
        margin=dict(l=20, r=20, t=50, b=20),
        paper_bgcolor='rgba(0,0,0,0)',
        plot_bgcolor='rgba(0,0,0,0)',
        font=dict(size=14),
    )

    # Display plot with selection enabled
    st.plotly_chart(fig, use_container_width=True)

    # Add lasso/box selection tool and capture selection
    st.write("Use the lasso or box tool in the plot to select data points.")
    selected_points = st.plotly_chart(
        fig,
        use_container_width=True,
        theme="streamlit",
        key='plot1',
        on_select="rerun",
        selection_mode=['lasso', 'box']
    )

    # Check if selection exists
    if selected_points and selected_points.selection:
        selected_indices = selected_points.selection["point_indices"]

        # Filter dataframe based on selection
        filtered_df = df.iloc[selected_indices]

        st.write('### Filtered Data')
        st.dataframe(filtered_df)

        # Optionally, create another plot for filtered data
        st.write('### Scatter Plot of Filtered Data')
        filtered_fig = px.scatter(
            filtered_df,
            x=x_axis,
            y=y_axis,
            color=color_option if color_option else None,
            size=size_option if size_option else None,
            hover_data=hover_data_options,
            title=f'Filtered Scatter Plot of {y_axis} vs {x_axis}'
        )
        st.plotly_chart(filtered_fig, use_container_width=True)

    else:
        st.info('Select points using the lasso or box tool to filter data.')

else:
    st.info("👈 Please upload a file to begin.")

    # Footer
st.markdown("""
    <div style="position: fixed; bottom: 0; width: 100%; text-align: center; padding: 10px; background-color: #0e1117;">
        <p>|Developed with ❤️ by Ashwin Nair | 
    </div>
    """, unsafe_allow_html=True)
