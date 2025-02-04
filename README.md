import streamlit as st
import json
import pandas as pd

hide_menu_style = """
    <style>
    #xMainMenu {visibility: hidden;} 
    .stAppDeployButton {visibility: hidden;} 
    .xstAppToolbar {visibility: hidden;} 
    </style>
"""
st.markdown(hide_menu_style, unsafe_allow_html=True)

# Загрузка JSON
with open('combined_logs.json') as f:
    data = json.load(f)

# Преобразование в DataFrame
df = pd.DataFrame(data).T

# Интерфейс
st.title("Панель управления серверами")

# Фильтры
st.sidebar.header("Фильтры")
stend_filter = st.sidebar.multiselect("Выберите стенд", df['stend'].unique())
po_filter = st.sidebar.multiselect("Выберите ПО", df['po'].unique())

# Применение фильтров
filtered_df = df
if stend_filter:
    filtered_df = filtered_df[filtered_df['stend'].isin(stend_filter)]
if po_filter:
    filtered_df = filtered_df[filtered_df['po'].isin(po_filter)]

# Таблица с данными
st.dataframe(filtered_df)

# Детальная информация
if st.checkbox("Показать подробности"):
    selected_host = st.selectbox("Выберите хост", filtered_df['hostname'])
    st.json(data[next(key for key, val in data.items() if val['hostname'] == selected_host)])
