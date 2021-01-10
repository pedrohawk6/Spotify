# ---------------------  Search Tracks and Artists in Spotify API ------------------------


#Get user and authorization information
import base64
import requests
import json


##   -----------   Part I of main function


 #1. From dashboard applications in developer.spotify.com/dashboard/applications get user information

client_id = 'XXXXXXXXXXXX'
client_secret = 'XXXXXXXXXXXXXX'

 # 2. Authorization in Spotify API to get access_token

def authorization_client_cred(client_id, client_secret):
    client_creds = f"{client_id}:{client_secret}"
    client_creds_b64 = base64.b64encode(client_creds.encode())     # convert it into bytes AND b64 especifically as requested in API Documentation

    token_url = 'https://accounts.spotify.com/api/token'
    method = "POST"
    token_data = {
        "grant_type": "client_credentials"
    }
    token_headers = {
        "Authorization": f"Basic {client_creds_b64.decode()}"          #<base64 encoded client_id:client_secret>
        }

    r = requests.post(token_url, data=token_data, headers=token_headers)
    print(r.json())

    access_token = r.json()['access_token']
    print('The access token is:', access_token)
    print('\n')
    return(access_token)

access_token = authorization_client_cred(client_id, client_secret)   #Running the function and storing access_token in variable


## Checks if a track is already in SQLite database or not

def check_track_in_db(ID_Track):                  #This function is called in insert tracks function

    import sqlite3
    ##connecting to the database
    conn_2 = sqlite3.connect('Spotify.db')
    ## getting a cursor to do some operations
    cur_2 = conn_2.cursor()

    ## structure to use the query code -- use the ? and (string,)
    cur_2.execute('SELECT * FROM Tracks WHERE ID_Track = ?', (ID_Track,))
    ## turns this into a list format (I think)
    tracks_db = cur_2.fetchall()
    #print(tracks_db)
  
    ## close the connection to the data base - best practice for safety       obs:(lookup how to save if we decide to make changes to db)
    conn_2.close()

    ## in case the ID is not in data base, return False, else True
    if tracks_db == []:
        print('False. Track not in db')
        return False  #Track is not in db yet

    else:
        print('True. Track already in db')
        return True   #Track is already in db
        


## Insert Spotify Tracks Info in SQLite database

def insert_tracks_db(ID_Track, Name_Track, Popularity, ID_Artist):
    
    import sqlite3
    ##connecting to the database
    conn_1 = sqlite3.connect('Spotify.db')
    ## getting a cursor to do some operations
    cur_1 = conn_1.cursor()

    ## Before adding the track to db, we check to make sure it isn't already there with a function
    is_track_there = check_track_in_db(ID_Track)

    if is_track_there == True:           #basically do nothing (save and close)
        # Save
        conn_1.commit()

        ## close the connection to the data base - best practice for safety       obs:(lookup how to save if we decide to make changes to db)
        conn_1.close()

        print('not adding the Track data')
        
    else:                                #insert the data
        
        ## INSERT new row of Data -- use the ? and (string,)
        cur_1.execute('INSERT INTO Tracks (ID_Track, Name_Track, Popularity, ID_Artist) VALUES (?, ?, ?, ?)', (ID_Track, Name_Track, Popularity, ID_Artist,) )

        # Save (commit) the changes
        conn_1.commit()

        ## close the connection to the data base - best practice for safety       obs:(lookup how to save if we decide to make changes to db)
        conn_1.close()
        print('adding the Track data')

## Checks if an Artist is already in SQLite database or not

def check_Artist_in_db(ID_Artist):                  #This function is called in insert Artists function

    import sqlite3
    ##connecting to the database
    conn_3 = sqlite3.connect('Spotify.db')
    ## getting a cursor to do some operations
    cur_3 = conn_3.cursor()

    ## structure to use the query code -- use the ? and (string,)
    cur_3.execute('SELECT * FROM Artists WHERE ID_Artist = ?', (ID_Artist,))
    ## turns this into a list format (I think)
    Artists_db = cur_3.fetchall()
    print(Artists_db)
  
    ## close the connection to the data base - best practice for safety       obs:(lookup how to save if we decide to make changes to db)
    conn_3.close()

    ## in case the ID is not in data base, return False, else True
    if Artists_db == []:
        print('False. Artist not in db')
        return False  #Artist is not in db yet

    else:
        print('True. Artist already in db')
        return True   #Artist is already in db



## Insert Spotify Artist Info in SQLite database

def insert_Artist_db(ID_Artist, Name_Artist):
    
    import sqlite3
    ##connecting to the database
    conn_4 = sqlite3.connect('Spotify.db')
    ## getting a cursor to do some operations
    cur_4 = conn_4.cursor()

     ## Before adding the Artist to db, we check to make sure it isn't already there with a function
    is_artist_there = check_Artist_in_db(ID_Artist)

    if is_artist_there == True:           #basically do nothing (save and close)
        # Save
        conn_4.commit()

        ## close the connection to the data base - best practice for safety       obs:(lookup how to save if we decide to make changes to db)
        conn_4.close()

        print('not adding the Artist data')

    else:                                #insert the data

        ## INSERT new row of Data -- use the ? and (string,)
        cur_4.execute('INSERT INTO Artists (ID_Artist, Name_Artist) VALUES (?, ?)', (ID_Artist, Name_Artist,) )

        # Save (commit) the changes
        conn_4.commit()

        ## close the connection to the data base - best practice for safety       obs:(lookup how to save if we decide to make changes to db)
        conn_4.close()

        print('adding the Artist data')


## Get an Artist's Top Tracks in a certain country

def artist_top_tracks(artist_id, artist_country_iso, access_token):

    artist_tracks_url = f"https://api.spotify.com/v1/artists/{artist_id}/top-tracks?country={artist_country_iso}"

    artist_tracks_headers = {
        "Authorization": f"Bearer {access_token}"
        }

    r2 = requests.get(artist_tracks_url, headers=artist_tracks_headers)
    print(r2.json())
    print('\n')

    artist_tracks_list = r2.json()['tracks']
    
    #print(artist_tracks_list)   and   insert new tracks to db
    for track_data in artist_tracks_list:
        print('Track:',track_data['name'],' popularity:',track_data['popularity'],'track_id:',track_data['id'], 'artist_id:', artist_id)

        insert_tracks_db(track_data['id'], track_data['name'], track_data['popularity'], artist_id)
        ## Example:   insert_tracks_db('3ZOEytgrvLwQaqXreDs2Jx', "Can't Stop", 81, '0L8ExT028jH3ddEcZwqJJ5')
        

    print('\n')


## Search for a specific song from an Artist

def search_track_artist(track_name, artist_name):
    
    search_url = f"https://api.spotify.com/v1/search?q=artist:{artist_name}%20track:{track_name}&type=track"

    search_headers = {
            "Authorization": f"Bearer {access_token}"
            }

    r3 = requests.get(search_url, headers=search_headers)
    print(r3.json())
    print('\n')

    search_tracks_list = r3.json()['tracks']['items']
    for track_data in search_tracks_list:
            print('Track:',track_data['name'],' popularity:',track_data['popularity'],'track_id:',track_data['id'], 'artist_id:', track_data['artists'][0]['id'], 'artist_name:', track_data['artists'][0]['name'])

    ID_Track = search_tracks_list[0]['id']
    Name_Track = search_tracks_list[0]['name']
    Popularity = search_tracks_list[0]['popularity']
    ID_Artist = search_tracks_list[0]['artists'][0]['id']
    Name_Artist = search_tracks_list[0]['artists'][0]['name']

    #print('\n')
    #print(ID_Track)
    #print(Name_Track)
    #print(Popularity)
    #print(ID_Artist)
    #print(Name_Artist)

    search_dict = {'ID_Track': ID_Track, 'Name_Track': Name_Track, 'Popularity': Popularity, 'ID_Artist': ID_Artist, 'Name_Artist': Name_Artist}
    return search_dict



##  -----------   Part II of the main function


#artist_id = '0L8ExT028jH3ddEcZwqJJ5'      #Red Hot Chili Peppers id as example  ->   we can also get it in search_track_artist() function
artist_country_iso = 'BR'

artist_name = 'Red Hot Chili Peppers'
track_name = 'otherside'

search_dict = search_track_artist(track_name, artist_name)                #This runs one of the functions above (had to be after the variables are declared)

print(search_dict, '\n')
artist_id = search_dict['ID_Artist']

insert_Artist_db(artist_id, artist_name)                                 # also runs one of the functions above

artist_top_tracks(artist_id, artist_country_iso, access_token)           #this runs one of the other functions above


