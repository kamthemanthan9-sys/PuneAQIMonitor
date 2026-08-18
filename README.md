# projectf

This is a Flask-based landing page for the AQI Monitor application.

## Setup

1. Create and activate a Python virtual environment:
   ```powershell
   python -m venv venv
   .\venv\Scripts\Activate.ps1  # Windows PowerShell
   ```
2. Install dependencies:
   ```powershell
   pip install -r requirements.txt
   ```

## Running the app

Start the Flask server by running the control script:

```powershell
python PPControl.py
```

Then open your browser to `http://127.0.0.1:5000` (or the URL shown in the console). The landing page defined in `templates/landing_page.html` will be served.

## Development

- Modify `templates/landing_page.html` to change the UI (ensure the file is saved inside the `templates` directory — Flask won't find it otherwise).
- If you see a `TemplateNotFound` error, double‑check that the HTML file exists under `templates/` and that you are running the app from the project root.
- Add additional routes or logic to `PPControl.py` as required.


## Notes

- Flask automatically loads templates from the `templates` folder.
- If you prefer using the `flask` command, set `FLASK_APP=PPControl.py` and run `flask run`.
### Data storage

User login/registration information is now stored in a MongoDB collection. By default the app
connects to `mongodb://localhost:27017` but you can override this by setting the
`MONGODB_URI` environment variable before starting the server.

Ensure MongoDB is running (e.g. via `mongod` or using Atlas) so authentication works properly.

Profile picture uploads are saved under `static/uploads`; the directory is created
in the project root, and the database stores the relative path so images can be
served by Flask.

### Profile navigation

When a user logs in, the top‑right navigation area becomes a profile button
showing either initials or their profile picture. Clicking it now directs
straight to the `/edit-profile` page so the edit form appears immediately;
the standalone `/profile` view still exists (and contains an “Edit Profile”
button) but is no longer the default target. Profile information is cached
in `localStorage` for quicker UI updates between requests.

### AQI APIs

Two simple endpoints provide air quality data for Pune.

* `GET /api/aqi` – returns a small JSON object with the latest index, category
  and timestamp. The current implementation returns static sample values but
  you can replace it with real HTTP requests or sensor data if desired.
* `GET /api/aqi-map` – responds with a GeoJSON-style feature collection
  containing point(s) with AQI readings. Useful for mapping libraries.
* `GET /api/user` – returns the current session's profile data (`name`,
  `email`, `phone`, `profile_pic`, and now `location` & `bio`). Client-side
  code uses this to populate the UI and cache values in `localStorage`.

Use these endpoints from client-side code (e.g. `fetch`) or external scripts to
provide live AQI and user data.

### Editing profile

The profile page now includes an "Edit Profile" button which navigates
to a dedicated edit page (`/edit-profile`) containing a full form. Users can
modify name, phone number, location, bio and upload a new profile picture
from their device. Submitting the form sends a POST to `/update-profile`; the
new `/update-profile` route added in `PPControl.py` pulls values from the
request, saves them to the MongoDB users collection, and then refreshes the
Flask session.  Name, phone, location, bio and an uploaded picture are all
persisted.  The picture file is stored under `static/uploads` and its path is
stored in MongoDB so it can be served later.

Navigation links across the site have been updated so the avatar/button will
open the edit form directly.

The "Update Picture" overlay also triggers the file selector for convenience.
