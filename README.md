# Digit Derby

A dice race for the classroom. Students pick a racer, answer a maths question and roll. A right answer rolls a bonus die, and each of the 40 racers has its own power.

This folder is the whole game:

| File | What it is |
| --- | --- |
| `index.html` | The game. You never need to edit it. |
| `config.js` | Connection settings for invite codes (Supabase or Firebase). Empty by default. |
| `README.md` | This guide. |

There are two steps. Step 1 puts the game online so anyone can open it. Step 2 switches on invite codes so students can join the same race from their own devices. You can stop after step 1 and add step 2 later.

## Step 1: put the game on GitHub Pages

1. Sign in at github.com and create a new repository. Give it a short name such as `digit-derby` and make it **Public** (free GitHub Pages needs a public repository).
2. On the repository page choose **Add file → Upload files**, drag in `index.html`, `config.js` and `README.md`, then **Commit changes**.
3. Open **Settings → Pages**. Under “Build and deployment” choose **Deploy from a branch**, pick the **main** branch and the **/ (root)** folder, then **Save**.
4. Wait a minute or two. The same page will show your address, which looks like `https://your-username.github.io/digit-derby/`.

Open that address and the game is ready for same-screen play: up to 8 racers take turns on one device, which suits a projector or a small group around an iPad.

## Step 2: switch on invite codes

Joining from separate devices needs a service that passes messages between them. You can use **Supabase** (option A) or **Firebase** (option B). Pick one; you only set it up once.

### Option A: Supabase

Supabase’s Realtime feature passes the messages. You don’t need to create any tables, SQL or policies.

1. Sign in at supabase.com and open your project, or create a new one. A separate project just for the game is safest (see “Privacy” below).
2. Find your **Project URL**. It looks like `https://abcdefghijkl.supabase.co`. It’s shown on the project’s home page under **Connect**, and in **Project Settings → Data API**.
3. Find your **public key**. In **Project Settings → API Keys**, copy either the **publishable** key (starts with `sb_publishable_`) or the legacy **anon public** key (a long string starting with `eyJ`). **Never** use the `service_role` or `secret` key: those give full access to your project.
4. On GitHub, open `config.js`, click the pencil to edit, and paste the two values between the quotes:

   ```js
   supabaseUrl: "https://abcdefghijkl.supabase.co",
   supabaseKey: "sb_publishable_xxxxxxxxxxxxxxxx",
   ```

5. **Commit changes**, wait a couple of minutes, then reload the game with Ctrl+Shift+R (Cmd+Shift+R on a Mac).

If joining doesn’t work, open **Realtime → Settings** in Supabase and check that **public access** to channels is allowed. It is on for new projects, but it can be switched off.

### Option B: Firebase

1. Go to console.firebase.google.com and sign in with a Google account. Create a project (any name; you can turn Google Analytics off).
2. In the project, open **Build → Realtime Database** (not Firestore) and choose **Create Database**. Pick the location nearest you and start in **locked mode**.
3. Open the database’s **Rules** tab, replace what is there with the rules below, and **Publish**.

   ```json
   {
     "rules": {
       "rooms": {
         "$code": {
           ".read": true,
           ".write": true
         }
       }
     }
   }
   ```

4. Open the **Data** tab and copy the address shown at the top. It looks like `https://your-project-default-rtdb.firebaseio.com` or ends in `firebasedatabase.app`.
5. On GitHub, open `config.js`, click the pencil to edit, and paste the address between the quotes after `databaseURL:`.
6. **Commit changes**, wait a couple of minutes, then reload the game.

If both options are filled in, the game uses Supabase.

### Checking it worked

Reload the game. A box called “Got an invite code?” now appears on the home screen, and “Who’s racing?” has a button called “Let others join from their own devices”. If a yellow “Online play is off” box appears instead, it says what’s wrong with `config.js`.

Supabase’s and Firebase’s menus change from time to time, so a button may sit in a slightly different place from this description.

## Running a race with invite codes

1. One person sets up the race: choose the mode and the maths, then tap **Let others join from their own devices**. A four-letter code appears.
2. Everyone else opens the same web address on their own device, types the code and their name, and picks a racer. **Copy the join link** gives a link with the code already filled in.
3. Joined players show up in the “Who’s racing?” list, where you can set each one’s level or remove them.
4. Tap **Start the race**. Each player answers and rolls on their own device, and everyone sees the same board.

Things worth knowing:

- **The device that set up the race runs it.** Keep that page open. If it reloads, the home screen offers to continue the race.
- **Up to 8 racers per room.** For a whole class, run several rooms at once: each group has one person set up a room and share its code. You can also set up a room on the projector with no racers of your own and let up to 8 students or teams join it.
- **If someone’s device drops out**, the person who set up the race can skip their turn. A skip counts as running out of time, so that racer gets no answer and moves only on the main die. Nobody can answer a question for another player.
- **Mixed play works.** Racers on the host’s device, computer racers and joined players can all be in the same race.
- **A student who reloads** is offered a button to rejoin the room.

## Privacy and tidying up

- The game only ever sends game rooms: the names students type, their racers and the state of the race. Ask students to use first names or nicknames.
- **Supabase:** nothing is stored in your database. Messages pass through Supabase Realtime and are gone once delivered; the room lives on the device that set up the race. The public key is meant to be visible in web pages, but anyone with it can also read any tables in that project that don’t have row level security switched on. Use a separate Supabase project for the game, or check that every table has RLS enabled.
- **Firebase:** rooms are stored in your database. Anyone who knows its address could read or change a room, so don’t store anything else in that Firebase project. A room is deleted when its host closes it or sets up a new one. To clear everything, open the Firebase **Data** tab and delete `rooms`.
- Both services’ free plans limit how many devices can be connected at the same moment and how many messages can be sent. One class is well within those limits; check their pricing pages if you plan to run many classes at once.

## If something doesn’t work

- **No invite options on the home screen:** read the yellow “Online play is off” box, which names the problem. If there’s no box at all, GitHub Pages hasn’t refreshed yet, or `index.html` isn’t the one from this folder.
- **“Couldn’t reach the game server”:** check the values in `config.js` have no typos. For Supabase, check public channel access is allowed in Realtime settings. For Firebase, check the rules in option B were published. Some school networks block these services; try on another network to check.
- **“No room has that code”:** the host closed the room or set up a new one. Ask for the current code.
- **A header pill says “Reconnecting…”:** that device has lost its connection. It catches up by itself when the connection returns.
