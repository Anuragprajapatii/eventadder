<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Event Planner</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        /*
        *** IMPORTANT FIX FOR GITHUB PAGES MODAL ISSUE ***
        This rule ensures the modal is hidden immediately upon page load,
        addressing the "white box popup" issue before Tailwind CDN fully loads.
        */
        #eventDetailModal {
            display: none !important; 
        }

        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 1rem;
            box-sizing: border-box;
        }
        /* Custom scrollbar for event list */
        #eventList::-webkit-scrollbar {
            width: 8px;
        }
        #eventList::-webkit-scrollbar-track {
            background: #e0e7ff; /* Light indigo */
            border-radius: 10px;
        }
        #eventList::-webkit-scrollbar-thumb {
            background: #818cf8; /* Indigo 400 */
            border-radius: 10px;
        }
        #eventList::-webkit-scrollbar-thumb:hover {
            background: #6366f1; /* Indigo 500 */
        }
        /* Modal overlay */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }
        .modal-content {
            background-color: white;
            padding: 2rem;
            border-radius: 0.75rem; /* rounded-xl */
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05); /* shadow-lg */
            max-width: 90%;
            width: 400px;
            position: relative;
        }
    </style>
</head>
<body class="bg-gray-100 flex items-center justify-center min-h-screen p-4">

    <div class="bg-white p-6 md:p-8 rounded-xl shadow-lg w-full max-w-xl mx-auto border border-gray-200">
        <h1 class="text-3xl font-bold text-gray-800 mb-6 text-center">Event Planner</h1>

        <div class="mb-6 space-y-4">
            <div>
                <label for="eventName" class="block text-sm font-medium text-gray-700 mb-1">Event Name</label>
                <input type="text" id="eventName" placeholder="Enter event name"
                        class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-lg shadow-sm focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm transition duration-150 ease-in-out">
                <div class="flex flex-wrap gap-2 mt-2">
                    <button id="btnDestinationWedding" type="button"
                            class="bg-blue-100 hover:bg-blue-200 text-blue-800 text-xs px-3 py-1 rounded-full font-semibold transition duration-150 ease-in-out">
                        Destination Wedding
                    </button>
                    <button id="btnRoomsBooked" type="button"
                            class="bg-blue-100 hover:bg-blue-200 text-blue-800 text-xs px-3 py-1 rounded-full font-semibold transition duration-150 ease-in-out">
                        Rooms Booked
                    </button>
                </div>
            </div>

            <div>
                <label for="eventDescription" class="block text-sm font-medium text-gray-700 mb-1">Description (Optional)</label>
                <textarea id="eventDescription" rows="3" placeholder="Add a brief description of the event"
                              class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-lg shadow-sm focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm transition duration-150 ease-in-out resize-y"></textarea>
            </div>

            <div>
                <label for="dateFrom" class="block text-sm font-medium text-gray-700 mb-1">Date From</label>
                <input type="date" id="dateFrom"
                        class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-lg shadow-sm focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm transition duration-150 ease-in-out">
            </div>

            <div>
                <label for="dateTo" class="block text-sm font-medium text-gray-700 mb-1">Date To</label>
                <input type="date" id="dateTo"
                        class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-lg shadow-sm focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm transition duration-150 ease-in-out">
            </div>

            <button id="addEventBtn" disabled
                    class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2 px-4 rounded-lg shadow-md transition duration-200 ease-in-out transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 disabled:opacity-50 disabled:cursor-not-allowed">
                Add Event
            </button>
            <p id="authStatusMessage" class="text-center text-sm text-gray-600 mt-2">Loading authentication...</p>
        </div>

        <div id="messageBox" class="hidden bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded-lg relative mb-4" role="alert">
            <span id="messageText" class="block sm:inline"></span>
            <span class="absolute top-0 bottom-0 right-0 px-4 py-3 cursor-pointer" onclick="document.getElementById('messageBox').classList.add('hidden');">
                <svg class="fill-current h-6 w-6 text-red-500" role="button" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><title>Close</title><path d="M14.348 14.849a1.2 1.2 0 0 1-1.697 0L10 11.103l-2.651 2.646a1.2 1.2 0 1 1-1.697-1.697L8.303 9.406l-2.651-2.646a1.2 1.2 0 1 1 1.697-1.697L10 7.71l2.651-2.646a1.2 1.2 0 0 1 1.697 1.697L11.697 9.406l2.651 2.646a1.2 1.2 0 0 1 0 1.697z"/></svg>
            </span>
        </div>

        <div class="mt-8">
            <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">Upcoming Events</h2>

            <div class="mb-4 flex items-center space-x-2">
                <label for="filterDateInput" class="text-sm font-medium text-gray-700">Filter by Date:</label>
                <input type="date" id="filterDateInput"
                        class="flex-grow px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm">
                <button id="clearDateFilterBtn"
                        class="bg-gray-200 hover:bg-gray-300 text-gray-700 font-medium py-2 px-3 rounded-md text-sm transition duration-150 ease-in-out">
                    Clear
                </button>
            </div>

            <div id="eventList" class="space-y-3 max-h-64 overflow-y-auto pr-2">
                <p id="noEventsMessage" class="text-gray-500 text-center italic">No events added yet.</p>
            </div>
        </div>
    </div>

    <div id="eventDetailModal" class="modal-overlay hidden">
        <div class="modal-content">
            <h2 id="modalEventName" class="text-2xl font-bold text-gray-800 mb-4"></h2>
            <p class="text-gray-700 mb-2"><span class="font-semibold">From:</span> <span id="modalDateFrom"></span></p>
            <p class="text-gray-700 mb-4"><span class="font-semibold">To:</span> <span id="modalDateTo"></span></p>
            <p id="modalEventDescription" class="text-gray-700 mb-4 whitespace-pre-wrap"></p> <button id="closeModalBtn"
                    class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2 px-4 rounded-lg shadow-md transition duration-200 ease-in-out">
                Close
            </button>
        </div>
    </div>

    <script type="module">
        // --- Firebase SDK Imports (Version 12.0.0) ---
        import { initializeApp } from "https://www.gstatic.com/firebasejs/12.0.0/firebase-app.js";
        import { getAnalytics, logEvent } from "https://www.gstatic.com/firebasejs/12.0.0/firebase-analytics.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/12.0.0/firebase-auth.js";
        import { getDatabase, ref, push, remove, onValue, query, orderByChild, serverTimestamp, update } from "https://www.gstatic.com/firebasejs/12.0.0/firebase-database.js";

        // --- Global Firebase Variables ---
        let app;
        let db;
        let auth;
        let analytics;
        let currentUserId = null;
        let allEvents = []; // To store all fetched events for client-side filtering
        let filterDate = null; // Stores the current date filter

        // --- Your Firebase Configuration ---
        const firebaseConfig = {
            apiKey: "AIzaSyBqjLWOahOwNE_lQ1ek9YddNztzdfJYMiM",
            authDomain: "my-busssiness-hrr.firebaseapp.com",
            databaseURL: "https://my-busssiness-hrr-default-rtdb.firebaseio.com",
            projectId: "my-busssiness-hrr",
            storageBucket: "my-busssiness-hrr.firebasestorage.app",
            messagingSenderId: "426018891071",
            appId: "1:426018891071:web:7ad54c177f30f446da1f8a",
            measurementId: "G-15CEW6N7N5"
        };

        const appId = firebaseConfig.appId;
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // Get references to HTML elements
        const eventNameInput = document.getElementById('eventName');
        const btnDestinationWedding = document.getElementById('btnDestinationWedding');
        const btnRoomsBooked = document.getElementById('btnRoomsBooked');
        const eventDescriptionInput = document.getElementById('eventDescription');
        const dateFromInput = document.getElementById('dateFrom');
        const dateToInput = document.getElementById('dateTo');
        const addEventBtn = document.getElementById('addEventBtn');
        const eventList = document.getElementById('eventList');
        const noEventsMessage = document.getElementById('noEventsMessage');
        const messageBox = document.getElementById('messageBox');
        const messageText = document.getElementById('messageText');
        const authStatusMessage = document.getElementById('authStatusMessage');

        // Modal elements
        const eventDetailModal = document.getElementById('eventDetailModal');
        const modalEventName = document.getElementById('modalEventName');
        const modalDateFrom = document.getElementById('modalDateFrom');
        const modalDateTo = document.getElementById('modalDateTo');
        const modalEventDescription = document.getElementById('modalEventDescription');
        const closeModalBtn = document.getElementById('closeModalBtn'); // Ref for close button

        // Date Filter elements
        const filterDateInput = document.getElementById('filterDateInput');
        const clearDateFilterBtn = document.getElementById('clearDateFilterBtn');

        // --- Quick-select buttons event listeners ---
        btnDestinationWedding.addEventListener('click', () => {
            eventNameInput.value = 'Destination Wedding';
        });

        btnRoomsBooked.addEventListener('click', () => {
            eventNameInput.value = 'Rooms Booked';
        });

        // --- Date Filter event listeners ---
        filterDateInput.addEventListener('input', () => {
            filterDate = filterDateInput.value;
            renderEvents(allEvents); // Re-render with the new filter
        });

        clearDateFilterBtn.addEventListener('click', () => {
            filterDateInput.value = '';
            filterDate = null;
            renderEvents(allEvents); // Re-render to show all events
        });

        // --- Modal close button event listener ---
        closeModalBtn.addEventListener('click', () => {
            eventDetailModal.classList.add('hidden'); // This is the line that hides the modal
        });


        // --- Utility Functions ---
        function showMessage(message, type = 'error') {
            messageText.textContent = message;
            messageBox.classList.remove('hidden');
            if (type === 'error') {
                messageBox.classList.remove('bg-green-100', 'border-green-400', 'text-green-700');
                messageBox.classList.add('bg-red-100', 'border-red-400', 'text-red-700');
            } else if (type === 'success') {
                messageBox.classList.remove('bg-red-100', 'border-red-400', 'text-red-700');
                messageBox.classList.add('bg-green-100', 'border-green-400', 'text-green-700');
            }
            setTimeout(() => {
                hideMessage();
            }, 5000);
        }

        function hideMessage() {
            messageBox.classList.add('hidden');
        }

        function formatFriendlyDate(dateString) {
            if (!dateString) return '';
            const date = new Date(dateString + 'T00:00:00');
            const options = { year: 'numeric', month: 'long', day: 'numeric' };
            return date.toLocaleDateString(undefined, options);
        }

        // --- Realtime Database Listener Function ---
        function setupEventListener() {
            if (!currentUserId) {
                console.warn("User not authenticated yet, cannot set up Realtime Database listener.");
                return;
            }

            const eventsRef = ref(db, `artifacts/${appId}/users/${currentUserId}/events`);

            onValue(query(eventsRef, orderByChild('timestamp')), (snapshot) => {
                allEvents = []; // Reset the global array
                snapshot.forEach((childSnapshot) => {
                    allEvents.push({ id: childSnapshot.key, ...childSnapshot.val() });
                });
                renderEvents(allEvents); // Render all events, filtering will happen inside renderEvents
            }, (error) => {
                console.error("Error fetching events from Realtime Database: ", error);
                showMessage('Error loading events. Please refresh the page.', 'error');
            });
        }

        // --- Firebase Initialization and Authentication Function ---
        async function initializeFirebaseAndAuth() {
            if (Object.keys(firebaseConfig).length === 0) {
                console.error("Firebase config is missing or empty. Events will not be saved.");
                return;
            }

            app = initializeApp(firebaseConfig);
            db = getDatabase(app);
            auth = getAuth(app);
            analytics = getAnalytics(app);

            onAuthStateChanged(auth, async (user) => {
                if (user) {
                    currentUserId = user.uid;
                    addEventBtn.disabled = false;
                    authStatusMessage.textContent = 'Authenticated. Ready to add events!';
                    authStatusMessage.classList.remove('text-gray-600', 'text-red-700');
                    authStatusMessage.classList.add('text-green-700');
                    setupEventListener();
                } else {
                    currentUserId = null;
                    addEventBtn.disabled = true;
                    authStatusMessage.textContent = 'Not authenticated. Please wait or sign in.';
                    authStatusMessage.classList.remove('text-gray-600', 'text-green-700');
                    authStatusMessage.classList.add('text-red-700');
                    eventList.innerHTML = '<p class="text-gray-500 text-center italic">Sign in to see your events.</p>';
                    noEventsMessage.classList.add('hidden');
                }
            });

            try {
                if (initialAuthToken) {
                    await signInWithCustomToken(auth, initialAuthToken);
                } else {
                    await signInAnonymously(auth);
                }
                logEvent(analytics, 'app_started');
            } catch (e) {
                console.error("Error during Firebase authentication:", e);
                showMessage('Authentication failed. Events may not be saved.', 'error');
                addEventBtn.disabled = true;
                authStatusMessage.textContent = 'Authentication failed. Please check console.';
                authStatusMessage.classList.remove('text-gray-600', 'text-green-700');
                authStatusMessage.classList.add('text-red-700');
            }
        }

        // --- CRUD Operations for Realtime Database ---
        async function addEventToFirebase(eventName, eventDescription, dateFrom, dateTo) {
            if (!currentUserId) {
                showMessage('Authentication not ready. Please wait.', 'error');
                return;
            }

            try {
                const userEventsRef = ref(db, `artifacts/${appId}/users/${currentUserId}/events`);

                await push(userEventsRef, {
                    eventName: eventName,
                    eventDescription: eventDescription,
                    dateFrom: dateFrom,
                    dateTo: dateTo,
                    isPaid: false, // Default to not paid
                    timestamp: serverTimestamp()
                });

                showMessage('Event added successfully!', 'success');
                eventNameInput.value = '';
                eventDescriptionInput.value = '';
                dateFromInput.value = '';
                dateToInput.value = '';

            } catch (e) {
                console.error("Error adding event to Realtime Database: ", e);
                showMessage('Error adding event. Please try again.', 'error');
            }
        }

        async function toggleEventPaidStatus(eventId, currentIsPaidStatus) {
            if (!currentUserId) {
                showMessage('Authentication not ready. Please wait.', 'error');
                return;
            }

            try {
                const eventRef = ref(db, `artifacts/${appId}/users/${currentUserId}/events/${eventId}`);
                await update(eventRef, {
                    isPaid: !currentIsPaidStatus // Toggle the status
                });
                showMessage(`Event marked as ${!currentIsPaidStatus ? 'Paid' : 'Unpaid'}.`, 'success');
            } catch (e) {
                console.error("Error updating paid status: ", e);
                showMessage('Error updating paid status. Please try again.', 'error');
            }
        }

        async function deleteEventFromFirebase(eventId) {
            if (!currentUserId) {
                showMessage('Authentication not ready. Please wait.', 'error');
                return;
            }

            try {
                const eventToDeleteRef = ref(db, `artifacts/${appId}/users/${currentUserId}/events/${eventId}`);
                await remove(eventToDeleteRef);
                showMessage('Event deleted successfully.', 'success');
            } catch (e) {
                console.error("Error deleting event from Realtime Database: ", e);
                showMessage('Error deleting event. Please try again.', 'error');
            }
        }

        // --- Function to Render Events (called by RTDB listener and filter) ---
        function renderEvents(events) {
            eventList.innerHTML = '';

            let eventsToRender = [...events]; // Create a copy of all events

            // Apply date filter if one is set
            if (filterDate) {
                eventsToRender = eventsToRender.filter(event => {
                    // Check if the filterDate falls within the event's date range
                    return event.dateFrom <= filterDate && event.dateTo >= filterDate;
                });
            }

            if (eventsToRender.length === 0) {
                noEventsMessage.classList.remove('hidden');
                // Adjust message if a filter is active
                if (filterDate) {
                    noEventsMessage.textContent = `No events found for ${formatFriendlyDate(filterDate)}.`;
                } else {
                    noEventsMessage.textContent = 'No events added yet.';
                }
            } else {
                noEventsMessage.classList.add('hidden');
                eventsToRender.forEach(event => {
                    const eventItem = document.createElement('div');
                    eventItem.classList.add(
                        'bg-indigo-50', 'p-4', 'rounded-lg', 'shadow-sm', 'border', 'border-indigo-200',
                        'flex', 'flex-col', 'md:flex-row', 'justify-between', 'items-start', 'md:items-center', 'gap-2'
                    );

                    const eventDetails = document.createElement('div');
                    eventDetails.classList.add('mb-2', 'md:mb-0', 'cursor-pointer', 'flex-grow');
                    eventDetails.setAttribute('data-event-name', event.eventName || '');
                    eventDetails.setAttribute('data-date-from', event.dateFrom || '');
                    eventDetails.setAttribute('data-date-to', event.dateTo || '');
                    eventDetails.setAttribute('data-event-description', event.eventDescription || '');

                    const eventTitle = document.createElement('h3');
                    eventTitle.classList.add('text-lg', 'font-semibold', 'text-indigo-800');
                    eventTitle.textContent = event.eventName || 'No Name';

                    const eventDates = document.createElement('p');
                    eventDates.classList.add('text-sm', 'text-indigo-700');
                    eventDates.textContent = `${formatFriendlyDate(event.dateFrom)} to ${formatFriendlyDate(event.dateTo)}`;

                    eventDetails.appendChild(eventTitle);
                    eventDetails.appendChild(eventDates);

                    eventDetails.addEventListener('click', () => {
                        modalEventName.textContent = event.eventName || 'No Name';
                        modalDateFrom.textContent = formatFriendlyDate(event.dateFrom);
                        modalDateTo.textContent = formatFriendlyDate(event.dateTo);
                        modalEventDescription.textContent = event.eventDescription ? `Description: ${event.eventDescription}` : 'No description provided.';
                        eventDetailModal.classList.remove('hidden'); // Show the modal
                        hideMessage(); // Hide any general messages when modal opens
                    });

                    // --- Paid Button ---
                    const paidButton = document.createElement('button');
                    paidButton.classList.add('font-medium', 'py-1', 'px-3', 'rounded-md', 'text-xs', 'transition', 'duration-150', 'ease-in-out', 'flex-shrink-0');
                    if (event.isPaid) {
                        paidButton.textContent = 'Paid';
                        paidButton.classList.add('bg-green-500', 'hover:bg-green-600', 'text-white');
                        paidButton.classList.remove('bg-yellow-500');
                    } else {
                        paidButton.textContent = 'Mark Paid';
                        paidButton.classList.add('bg-yellow-500', 'hover:bg-yellow-600', 'text-white');
                        paidButton.classList.remove('bg-green-500');
                    }
                    paidButton.addEventListener('click', () => {
                        toggleEventPaidStatus(event.id, event.isPaid);
                    });

                    // --- Delete Button ---
                    const deleteButton = document.createElement('button');
                    deleteButton.classList.add('bg-red-500', 'hover:bg-red-600', 'text-white', 'font-medium', 'py-1', 'px-3', 'rounded-md', 'text-xs', 'transition', 'duration-150', 'ease-in-out', 'flex-shrink-0');
                    deleteButton.textContent = 'Delete';
                    deleteButton.addEventListener('click', () => {
                        deleteEventFromFirebase(event.id);
                    });

                    // Group buttons in a container for better layout
                    const buttonContainer = document.createElement('div');
                    buttonContainer.classList.add('flex', 'gap-2', 'items-center', 'md:flex-row');
                    buttonContainer.appendChild(paidButton);
                    buttonContainer.appendChild(deleteButton);

                    eventItem.appendChild(eventDetails);
                    eventItem.appendChild(buttonContainer);

                    eventList.appendChild(eventItem);
                });
            }
        }

        // --- Main Event Listeners (Add Event) ---
        addEventBtn.addEventListener('click', () => {
            hideMessage();

            const eventName = eventNameInput.value.trim();
            const eventDescription = eventDescriptionInput.value.trim();
            const dateFrom = dateFromInput.value;
            const dateTo = dateToInput.value;

            if (!eventName || !dateFrom || !dateTo) {
                showMessage('Please fill in Event Name, Date From, and Date To.');
                return;
            }

            const fromDate = new Date(dateFrom);
            const toDate = new Date(dateTo);

            if (fromDate > toDate) {
                showMessage('End Date cannot be before Start Date.');
                return;
            }

            addEventToFirebase(eventName, eventDescription, dateFrom, dateTo);
        });
        
        // --- Initial Call to Start Firebase ---
        initializeFirebaseAndAuth();
    </script>
</body>
</html>
