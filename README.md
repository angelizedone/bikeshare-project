# bikeshare-project
bikeshare data investigation
import time
import pandas as pd
import numpy as np

CITY_DATA = { 'ch': 'chicago.csv',
              'ny': 'new_york_city.csv',
              'wt': 'washington.csv' }

def get_filters():
    """
    Asks user to specify a city, month, and day to analyze.

    Returns:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    """
    print('Hello! Let\'s explore some US bikeshare data! \n')
    # get user input for city (chicago, new york city, washington). HINT: Use a while loop to handle invalid inputs
    print('='*20)
    print("Would you like to see data for Chicago, New York, or Washington?\n")
    print("Kindly type CH for Chicago ,NY for New York and WT for Washington ")
    city = input("Please enter your choice: \n ").lower()
    while city not in CITY_DATA.keys():
        print("Invalid city name..")
        print("Kindly type CH for Chicago ,NY for New York and WT for Washington :\n ")
        city = input().lower()
    print('-'*20)
    # get user input for month (all, january, february, ... , june)
    month_data = ['jan', 'feb', 'mar', 'apr', 'may', 'jun', 'all']
    print("Kindly type jan, feb, mar, apr, may, jun or all to seek the month to be filtered by \n")
    print("Remember that months range is from jan to jun only..")
    month = input("Write your month input exactly as writen above :\n ").lower()
    while month not in month_data:
        print("Invalid month name..")
        print("Remember write your month input exactly as writen above :\n ")
        month = input().lower()
    # get user input for day of week (all, monday, tuesday, ... sunday)
    print('-'*20)
    day_data = ['sunday', 'monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'all']
    day = ''
    print("Kindly type the day of week for that data you want to seek \n ")
    print("Type your input as a word e.g sunday & type 'all' to seek all days ")
    day = input("Please enter the day : \n ").lower()
    while day not in day_data:
        print("Invalid day input")
        print("Remember to type your input as a word e.g sunday & type 'all' to seek all days \n")
        day = input().lower()
    print('-'*40)
    return city, month, day


def load_data(city, month, day):
    """
    Loads data for the specified city and filters by month and day if applicable.

    Args:
        (str) city - name of the city to analyze
        (str) month - name of the month to filter by, or "all" to apply no month filter
        (str) day - name of the day of week to filter by, or "all" to apply no day filter
    Returns:
        df - Pandas DataFrame containing city data filtered by month and day
    """
    df = pd.read_csv(CITY_DATA[city])
    df['Start Time'] = pd.to_datetime(df['Start Time'])
    df['month'] = df['Start Time'].apply(lambda x: x.strftime('%b').lower())
    df['day_of_week'] = df['Start Time'].apply(lambda d: d.strftime('%A').lower())
    if month != 'all':
        df = df[df['month'] == month]

    if day != 'all':
        df = df[df['day_of_week'] == day]

    return df


def time_stats(df):
    """Displays statistics on the most frequent times of travel."""

    print('\nCalculating The Most Frequent Times of Travel...\n')
    start_time = time.time()

    # display the most common month
    print('The most common month (from jan to jun) is :\n ',df['month'].mode()[0])

    # display the most common day of week
    print('The most common day of week is :\n ',df['day_of_week'].mode()[0])

    # display the most common start hour
    df['hour'] = df['Start Time'].dt.hour

    common_hour = df['hour'].mode()[0]
    print('The most common start hour is :\n ', common_hour)

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def station_stats(df):
    """Displays statistics on the most popular stations and trip."""

    print('\nCalculating The Most Popular Stations and Trip...\n')
    start_time = time.time()

    # display most commonly used start station
    print('The most common trip start is from : ',df['Start Station'].mode()[0])

    # display most commonly used end station
    print('The most common trip destination is to : ',df['End Station'].mode()[0])

    # display most frequent combination of start station and end station trip
    df['Full trip'] = df['Start Station'].str.cat(df['End Station'], sep =' to ')
    print('The most frequent trip start and end is from :\n ',df['Full trip'].mode()[0])
    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def trip_duration_stats(df):
    """Displays statistics on the total and average trip duration."""

    print('\nCalculating Trip Duration...\n')
    start_time = time.time()

    # display total travel time
    total_d = df['Trip Duration'].sum()
    day_t = total_d // (24 * 3600)
    total_d = total_d % (24 * 3600)
    hour_t = total_d // 3600
    total_d %= 3600
    minutes_t = total_d // 60
    total_d %= 60
    seconds_t = total_d

    print("Total travel time is ", day_t,"days", hour_t, "hours", minutes_t, "minutes", seconds_t, "seconds")

    # display mean travel time
    average_d = df['Trip Duration'].mean()
    day_v = average_d // (24 * 3600)
    average_d = average_d % (24 * 3600)
    hour_v = average_d // 3600
    average_d %= 3600
    minutes_v = average_d // 60
    average_d %= 60
    seconds_v = average_d

    print("Average travel time is ", day_v,"days", hour_v, "hours", minutes_v, "minutes", seconds_v, "seconds")

    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)


def user_stats(df):
    """Displays statistics on bikeshare users."""

    print('\nCalculating User Stats...\n')
    start_time = time.time()

    # Display counts of user types
    user_types = df['User Type'].value_counts()
    print("The users types are listed below :\n", user_types)
    print('-'*20)
    # Display counts of gender
    try:
        gender_count = df.groupby(['Gender']).size()
        print("The users gender is listed below :\n", gender_count)
    except:
        print("gender is not defined")
    print('-'*20)
    # Display earliest, most recent, and most common year of birth
    try:
        earliest = int(df['Birth Year'].min())
        most_recent = int(df['Birth Year'].max())
        most_common = int(df['Birth Year'].mode()[0])
        print("And for users birth year statistics \n",
              "\n The earliest birth year is :",earliest,
              "\n,The most recent birth year is :",most_recent,
              "\n  And finally, The most common birth year is :",most_common)
    except:
        print("Birth year is not defined")


    print("\nThis took %s seconds." % (time.time() - start_time))
    print('-'*40)
    view_data = input('\nWould you like to view 5 rows of individual trip data? Enter yes or no\n').lower()
    start_loc = 0
    while view_data == "yes":
        print(df.iloc[start_loc : start_loc+5])
        start_loc += 5
        view_data = input("Do you wish to continue?: ").lower()

def main():
    while True:
        city, month, day = get_filters()
        df = load_data(city, month, day)

        time_stats(df)
        station_stats(df)
        trip_duration_stats(df)
        user_stats(df)

        restart = input('\nWould you like to restart? Enter yes or no.\n')
        if restart.lower() != 'yes':
            break


if __name__ == "__main__":
	main()


