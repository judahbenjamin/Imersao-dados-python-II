# Pasta dedicada a exercícios práticos e desafios da Imersão de Dados com Python

Desafio da Aula 3 

import pycountry
import plotly.express as px

# Função para converter ISO-2 para ISO-3
def iso2_to_iso3(code):
  try:
    return pycountry.countries.get(alpha_2=code).alpha_3
  except:
    return None

# Função para converter ISO-3 para nome comum do país
def iso3_to_common_name(code):
  try:
    return pycountry.countries.get(alpha_3=code).common_name
  except:
    return code # Retorna o código se não encontrar

# Criar nova coluna com código ISO-03
df_limpo['residencia_iso3'] = df_limpo['residencia'].apply(iso2_to_iso3)

#Calcular média salarial por país (ISO-3)
df_ds = df_limpo[df_limpo['cargo'] == 'Data Scientist']
media_ds_pais = df_ds.groupby('residencia_iso3')['usd'].mean().round(0).astype(int).reset_index()

# Adicionar nome comum do país para o hover
media_ds_pais['country_name'] = media_ds_pais['residencia_iso3'].apply(iso3_to_common_name)

# Gerar o mapa
fig = px.choropleth(media_ds_pais,
                    locations='residencia_iso3',
                    color='usd',
                    color_continuous_scale='viridis', # Mudança para paleta 'viridis'
                    range_color=[media_ds_pais['usd'].min(), media_ds_pais['usd'].max()], # Definir range de cores dinamicamente
                    hover_name='country_name', # Mostrar nome completo do país no hover
                    hover_data={'usd': ':, .0f'}, # Mostrar salário formatado no hover
                    title='Salário médio de Cientista de Dados por País',
                    labels={'usd': 'Salário médio (USD)', 'residencia_iso3':'país'})

fig.show()

#####################################################################################################
#Usando o gráfico de barras

fig_bar = px.bar(media_ds_pais,
                 x='residencia_iso3',
                 y='usd',
                 title='Salário médio de Cientista de Dados por país',
                 labels={'usd':'Salário médio (USD)', 'residencia_iso3':'País'})
fig_bar.show()

######################################################################################################
