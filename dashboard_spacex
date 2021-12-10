# Import required libraries
import pandas as pd
import dash
import dash_html_components as html
import dash_core_components as dcc
from dash.dependencies import Input, Output
import plotly.express as px

# Read the airline data into pandas dataframe
spacex_df = pd.read_csv("spacex_launch_dash.csv")
max_payload = spacex_df['Payload Mass (kg)'].max()
min_payload = spacex_df['Payload Mass (kg)'].min()

# Create a dash application
app = dash.Dash(__name__)

# Create an app layout
app.layout = html.Div(children=[html.H1('SpaceX Launch Records Dashboard',
                                        style={'textAlign': 'center', 'color': '#503D36',
                                               'font-size': 40}),
                                # TASK 1: Add a dropdown list to enable Launch Site selection
                                # The default select value is for ALL sites
                                dcc.Dropdown(id='site-dropdown',
                                            options=[
                                                  {'label': 'All Sites', 'value': 'ALL'},
                                                  {'label': 'CCAFS LC-40', 'value': 'CCAFS LC-40'},
                                                  {'label': 'VAFB SLC-4E', 'value': 'VAFB SLC-4E'},
                                                  {'label': 'KSC LC-39A', 'value': 'KSC LC-39A'},
                                                  {'label': 'CCAFS SLC-40', 'value': 'CCAFS SLC-40'},
                                            ],
                                            value='ALL',
                                            placeholder="Select a Launch Site here",
                                            searchable=True
                                            ),
                                html.Br(),

                                # TASK 2: Add a pie chart to show the total successful launches count for all sites
                                # If a specific launch site was selected, show the Success vs. Failed counts for the site
                                # Function decorator to specify function input and output
                                html.Div([dcc.Graph(id='success-pie-chart')]),
                                html.Br(),

                                html.P("Payload range (Kg):"),
                                # TASK 3: Add a slider to select payload range
                                dcc.RangeSlider(id='payload-slider',
                                                min=0, max=10000, step=1000,
                                                marks={0: '0',
                                                       2500: '2500',
                                                       5000: '5000',
                                                       7500: '7500',
                                                       10000: '10000'},
                                                value=[min_payload, max_payload]),

                                # TASK 4: Add a scatter chart to show the correlation between payload and launch success
                                html.Div(dcc.Graph(id='success-payload-scatter-chart')),
                                ])

# TASK 2:
# Add a callback function for `site-dropdown` as input, `success-pie-chart` as output
@app.callback(Output(component_id='success-pie-chart', component_property='figure'),
             [Input(component_id='site-dropdown', component_property='value')])
def get_pie_chart(entered_site):
    #get a sub dataframe
    filtered_df = spacex_df[['Launch Site','class']]
    
    if entered_site == 'ALL':
        fig = px.pie(data_frame=filtered_df, 
                     names='Launch Site', 
                     title='Total Success Launches By Site')
        return fig
    else:
        site_df = pd.DataFrame()
        #create new dataframe of site-specific rows
        for index,row in filtered_df.iterrows():
            if row['Launch Site'] == entered_site:
                site_df = site_df.append(row)
        fig = px.pie(data_frame=site_df,
                     names='class', 
                     title=('Total Success Launches for site: ' + entered_site)
        )
        return fig


# TASK 4:
# Add a callback function for `site-dropdown` and `payload-slider` as inputs, `success-payload-scatter-chart` as output
@app.callback(Output(component_id='success-payload-scatter-chart', component_property='figure'),
             [Input(component_id='site-dropdown', component_property='value'), Input(component_id="payload-slider", component_property="value")])
def get_scatter_plot(entered_site, payload_bounds):
    min_payload = payload_bounds[0]
    max_payload = payload_bounds[1]
    filtered_df = pd.DataFrame()
    # filter out rows below min_payload and above max_payload
    for index,row in spacex_df.iterrows():
        if row['Payload Mass (kg)'] > min_payload and row['Payload Mass (kg)'] < max_payload:
            filtered_df = filtered_df.append(row)
    # plot all payloads
    if entered_site == 'ALL':
        plt = px.scatter(filtered_df, x='Payload Mass (kg)', y='class', color='Booster Version Category')
        return plt
    # plot site-specific payloads
    else:
        site_df = pd.DataFrame()
        # create new dataframe of site-specific rows
        for index,row in filtered_df.iterrows():
            if row['Launch Site'] == entered_site:
                site_df = site_df.append(row)
        plt = px.scatter(site_df, x='Payload Mass (kg)', y='class', color='Booster Version Category')
        return plt
# Run the app
if __name__ == '__main__':
    app.run_server()
