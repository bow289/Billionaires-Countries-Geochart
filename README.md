# Billionaires-Countries-Geochart
Tells how many billionaires live in what county from 2023

!pip install pycountry

import pandas as pd
import plotly.express as px

# Load your dataset
df = pd.read_csv(r"C:\Users\BoWil\Downloads\Billionaires Statistics Dataset.csv")

# Standardize country names
df['country'] = df['country'].str.title().str.strip()

# Count billionaires per country
country_counts = df['country'].value_counts().reset_index()
country_counts.columns = ['Country', 'Billionaire Count']

# Load all countries from ISO dataset for completeness
import pycountry
all_countries = pd.DataFrame({'Country': [country.name for country in pycountry.countries]})

# Merge billionaire data with full country list
merged_df = all_countries.merge(country_counts, on='Country', how='left')
merged_df['Billionaire Count'] = merged_df['Billionaire Count'].fillna(0)

# Create global choropleth map
fig = px.choropleth(
    merged_df,
    locations='Country',
    locationmode='country names',
    color='Billionaire Count',
    color_continuous_scale='Turbo',  # A vibrant color palette
    range_color=(0, merged_df['Billionaire Count'].max()),
    title='Global Distribution of Billionaires by Country',
    template='plotly_white'
)

fig.update_geos(showframe=False, showcoastlines=False, projection_type='natural earth')
fig.update_layout(margin=dict(l=0, r=0, t=50, b=0))

fig.show()
