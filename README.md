import streamlit as st
from mistralai import Mistral
import os

# Configuração de Interface Profissional Focus
st.set_page_config(page_title="Focus PRF IAQstron", layout="centered")

st.markdown("""
    <style>
    .stApp { background-color: #000000; color: #ffffff; }
    .card { 
        background: #111111; 
        padding: 25px; 
        border-radius: 12px; 
        border: 1px solid #333; 
        line-height: 1.8;
        box-shadow: 0 10px 30px rgba(0,0,0,0.5);
    }
    h2 { color: #00ff88; font-size: 1.1rem; text-transform: uppercase; margin-top: 25px; border-left: 4px solid #00ff88; padding-left: 10px; }
    .verbo { color: #fff; background: #cc0000; padding: 2px 6px; border-radius: 4px; font-weight: bold; }
    .sumula { color: #ffd700; font-weight: bold; text-decoration: underline; }
    b { color: #00ff88; }
    /* Esconder elementos poluídos */
    #MainMenu, footer, header {visibility: hidden;}
    </style>
    """, unsafe_allow_html=True)

# Conector Neural
api_key = os.getenv("MISTRAL_API_KEY", "6Hf8wwQJwEP36FsrNw9m7hY1hYbLAOAF")
client = Mistral(api_key=api_key)

st.markdown("<h1 style='text-align: center; color: #00ff88; font-family: monospace;'>FOCUS PRF IAQSTRON</h1>", unsafe_allow_html=True)

query = st.text_input("", placeholder="Qual tema vamos triturar hoje?")

if query:
    with st.spinner("Sincronizando base neural..."):
        try:
            prompt = f"Analise o tema {query} de forma fatiada para a PRF. Use HTML (h2, p, b). Verbos em <span class='verbo'> e Súmulas em <span class='sumula'>. PROIBIDO ASTERISCOS."
            resp = client.chat.complete(model="mistral-small-latest", messages=[{"role": "user", "content": prompt}])
            
            # Limpeza final
            answer = resp.choices[0].message.content.replace('*', '').replace('#', '')
            st.markdown(f"<div class='card'>{answer}</div>", unsafe_allow_html=True)
        except Exception as e:
            st.error(f"Erro de conexão: {e}")
