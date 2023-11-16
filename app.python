import os
from flask import Flask, render_template, request
from datetime import datetime, timedelta
from math import ceil
from dotenv import load_dotenv
load_dotenv()
google_api_key = os.environ.get('GOOGLE_API_KEY')


app = Flask(__name__)

# Get API key for Maps
google_api_key = os.environ.get('GOOGLE_API_KEY')

# Function to replace hyphen with en dash in hours
def replace_hours_with_ndash(market_info):
    for week, days in market_info.items():
        for day, markets in days.items():
            for market in markets:
                market['hours'] = market['hours'].replace('-', '&ndash;')


# Dictionary to hold the market information
markets = {
    1: {
        "Saturday": [
            {"name": "Churchill Island Farmers Market", "address": "246 Samuel Amess Dr, Churchill Island VIC 3925", "hours": "8.00am - 1.00pm"}
        ],
        "Sunday": [
            {"name": "Jumbunna Bush Market", "address": "Jumbunna Community Hall, Cruickshank Rd, Jumbunna VIC, Australia", "hours": "9.00am - 1.00pm"},
            {"name": "Koonwarra Farmers’ Market", "address": "Koonwarra Memorial Park<br />Koonwarra", "hours": "8.00am - 1.00pm"},
            {"name": "Kongwak Markets", "address": "1487 Korumburra-Wonthaggi Rd<br />Kongwak VIC 3951", "hours": "10.00am - 2.00pm"}
        ]
    },
    2: {
        "Saturday": [
            {"name": "Coal Creek Farmers' Market", "address": "Coal Creek Community Park & Museum<br />Silkstone Road, Korumburra VIC 3950", "hours": "8.00am - 12.30pm"},
            {"name": "Corinella Community Market", "address": "Hughes Reserve<br />Corner Smythe and Balcombe Streets, Corinella VIC 3984", "hours": "9.00am - 1.00pm"}
        ],
        "Sunday": [
            {"name": "Kongwak Markets", "address": "1487 Korumburra-Wonthaggi Rd<br />Kongwak VIC 3951", "hours": "10.00am - 2.00pm"}
        ]
    },
    3: {
        "Saturday": [
            {"name": "Inverloch Farmers' Market", "address": "The Glade, Inverloch", "hours": "8.00am - 1.00pm"},
            {"name": "Newhaven Market", "address": "Newhaven Primary School<br />22 School Avenue<br />Newhaven, VIC 3925", "hours": "9.00am - 2.00pm"},
            {"name": "Prom Country Farmers' Market", "address": "Main Street, Foster VIC 3960", "hours": "8.00am - 12.00pm"}
        ],
        "Sunday": [
            {"name": "Kongwak Markets", "address": "1487 Korumburra-Wonthaggi Rd<br />Kongwak VIC 3951", "hours": "10.00am - 2.00pm"}
        ]
    }, 
    4: {
        "Saturday": [
            {"name": "Cowes Market on Church", "address": "St Phillip's Parish Hall<br />Cowes", "hours": "9.00am - 2.00pm"},
            {"name": "Leongatha Community Market", "address": "Community College Gippsland<br />Howard Street, Leongatha VIC 3953", "hours": "8.30am - 12.30pm"}
        ],
        "Sunday": [
            {"name": "Inverloch Community Farmers' Market", "address": "TBC", "hours": "9.00am - 3.00pm"},
            {"name": "Kongwak Markets", "address": "1487 Korumburra-Wonthaggi Rd<br />Kongwak VIC 3951", "hours": "10.00am - 2.00pm"}
        ]
    }
}
# Replace hyphens in hours with en dashes
replace_hours_with_ndash(markets)


def get_week_of_month(dt):
    """ Function to get the week of the month for the specified date. 
    Returns 1 for dates in the fifth week of the month. """
    first_day = dt.replace(day=1)
    dom = dt.day
    adjusted_dom = dom + first_day.weekday()
    week_of_month = int(ceil(adjusted_dom/7.0))
    return week_of_month if week_of_month <= 4 else 1



def get_market_info(dt=None):
    """ Function to get the market information based on the provided date """
    if dt is None:
        dt = datetime.now()
    else:
        dt = datetime.strptime(dt, "%Y-%m-%d")

    week_of_month = get_week_of_month(dt)
    day_of_week = dt.strftime("%A")

    if day_of_week == "Saturday":
        return "Today", markets[week_of_month].get("Saturday", []), "Tomorrow", markets[week_of_month].get("Sunday", [])
    elif day_of_week == "Sunday":
        yesterday = dt - timedelta(days=1)
        yesterday_week_of_month = get_week_of_month(yesterday)
        return "Today", markets[week_of_month].get("Sunday", []), "Yesterday", markets[yesterday_week_of_month].get("Saturday", [])
    else:
        return "Saturday", markets[week_of_month].get("Saturday", []), "Sunday", markets[week_of_month].get("Sunday", [])

@app.route('/')
def index():
    date_param = request.args.get('date')
    saturday_label, saturday_markets, sunday_label, sunday_markets = get_market_info(date_param)

    # Move the API key retrieval here
    google_api_key = os.environ.get('GOOGLE_API_KEY')

    # Combine all necessary variables in a single return statement
    return render_template('index.html', saturday_label=saturday_label, 
                           saturday_markets=saturday_markets, 
                           sunday_label=sunday_label, 
                           sunday_markets=sunday_markets, 
                           google_api_key=google_api_key)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=os.environ.get('PORT', 5000))
