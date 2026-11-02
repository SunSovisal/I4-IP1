1. Refreshing /events/2 asks the Vite dev server on port 5173 for that URL. There is no file there, so Vite sends back index.html, the app starts, and Vue Router opens the detail page from the address. json-server only answers later, when the page fetches the event. A production server that only serves real files would 404 on /events/2. It has to return index.html for any path that is not a static file, and still send API calls to the backend.

2. Yes. The guard only runs in the browser, and the role is just a value in the session store. A student can skip the page and POST a new event to json-server with curl. The API has to refuse that unless the caller is an organiser. json-server never checks who is calling.

3. Moving from /events/2 to /events/3 reuses the same EventDetailPage, because only the id changed. onMounted runs once, when the component is first created, so the second event would never load and the page would keep showing the first one. The watch on eventId runs for every id, and immediate: true also covers the first load.

4. events, status and error are state on the store. const { events } = store copies them out, so after fetchAll the template can still be looking at the empty list from before the request. storeToRefs keeps those as refs, and the page updates when the store does. fetchAll is an action, so it stays on the store and is called as store.fetchAll().

5. If the POST is saved and the PATCH fails, the booking is in db.json but the event's booked count is still the old number. The page then offers seats that are already taken, and another booking can go through. The server should create the booking and update the seat count as one operation, and drop the booking if the seat update cannot be stored.
