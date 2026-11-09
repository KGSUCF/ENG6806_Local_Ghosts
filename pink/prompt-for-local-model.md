# Prompt for the local model

Copy everything between the two lines below and paste it into the Ollama chat.

---

Build a single-file web app called "In Search of Pink". Give me one complete HTML file with the CSS and JavaScript inside it, and no external libraries except Google Fonts. It must work when I double-click the file to open it in a browser.

**Purpose:** a phone-friendly list of Fort Lauderdale restaurants showing whether each one has pink Sweet'N Low packets in its sugar caddy. I use it at the table to check a restaurant quickly and to record what I find.

**Starting data.** Each restaurant has one of three answers: Pink, No, or Not checked.
- Pink: Doc B's, Tap 42, Moxies, YOLO, Foxy Brown, Blue Moon Fish, Fogo, Cheesecake Factory, Del Frisco's, Carrabba's, Lobster Bar
- No: Boathouse, YOT, Louie Bossi, Big City, El Camino, 60 Vines, Chimney House, Mykonos, S3, Seasons 52, Las Vegas
- Not checked: Worthwyld, American Social, Kaluz

**Features:**
1. A large title, "In Search of Pink", and a short subtitle explaining the purpose.
2. A search box that stays at the top of the screen as the list scrolls. Search should ignore capital letters, apostrophes, and spaces, so "docbs" finds "Doc B's". Highlight the matching letters.
3. Filter buttons: All, Pink, No, and Not checked, each showing a count.
4. An alphabetical list. Each row shows the name and a colored badge (pink for Pink, gray for No, striped for Not checked).
5. If a search finds nothing, offer a button to add that name as a new restaurant.
6. An "Add restaurant" button that opens a panel to type a name and choose an answer. Warn me if the restaurant is already on the list.
7. Tapping a restaurant opens a panel where I can change its answer, fix its name, or remove it (with a confirmation step). Show the date it was last changed.
8. After any change, show a short message with an Undo button.
9. Save everything in the browser's localStorage so the list is still there next time.
10. A menu with "Share the list", which turns the list into plain text grouped under PINK, NO PINK, and NOT CHECKED with buttons to copy or send it, and "Import a list", which reads that same text back in, adds new restaurants, and updates existing ones without deleting anything.

**Design:** mobile-first, with large tap targets and a cheerful pink color scheme. It must be easy to read, and it should switch to dark mode when the phone is in dark mode.

---
