import streamlit as st
import pandas as pd
import plotly.express as px

st.title("Dashboard de Orçamentos Residenciais")

# Upload do Excel
uploaded_file = st.file_uploader("Faça upload do arquivo Excel", type=["xlsx"])
if uploaded_file:
    df = pd.read_excel(uploaded_file, header=0)

    st.subheader("Dados Carregados")
    st.dataframe(df.head())

    # Filtros
    apt_types = df['Apartment type'].unique()
    services = df['Service'].unique()

    selected_apt = st.multiselect("Selecione o tipo de apartamento", apt_types, default=apt_types)
    selected_service = st.multiselect("Selecione o serviço", services, default=services)

    df_filtered = df[df['Apartment type'].isin(selected_apt) & df['Service'].isin(selected_service)]

    st.subheader("Resumo por Serviço e Tipo de Apartamento")
    summary = df_filtered.groupby(['Apartment type','Service']).agg({
        'Labour cost':'mean',
        'Material':'mean',
        'Equipment':'mean',
        'Subcontractor cost':'mean',
        'Total Cost':'mean'
    }).reset_index()

    st.dataframe(summary)

    # Gráfico interativo
    fig = px.bar(summary, x='Service', y='Total Cost', color='Apartment type', barmode='group',
                 title="Total Cost por Serviço e Tipo de Apartamento")
    st.plotly_chart(fig)
