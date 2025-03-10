<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SkyWatch - Enhanced</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary-color: #4CAF50;
            --secondary-color: #333;
            --text-color: #fff;
        }

        body {
            font-family: 'Roboto', sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            min-height: 100vh;
            background-color: #f0f0f0;
            transition: background-color 0.5s, color 0.5s;
        }

        header {
            background-color: var(--secondary-color);
            color: var(--text-color);
            text-align: center;
            padding: 1em;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }

        nav {
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
        }

        nav button {
            margin: 5px;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            background-color: var(--primary-color);
            color: var(--text-color);
            border: none;
            border-radius: 25px;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
        }

        nav button:hover {
            background-color: #45a049;
            transform: translateY(-2px);
        }

        nav button::before {
            font-family: 'Font Awesome 5 Free';
            margin-right: 5px;
        }

        #trackBtn::before { content: '\f072'; }
        #soundBtn::before { content: '\f028'; }
        #imageBtn::before { content: '\f03e'; }
        #reportBtn::before { content: '\f3c5'; }
        #identifyBtn::before { content: '\f05b'; }
        #learnBtn::before { content: '\f02d'; }
        #toggleTheme::before { content: '\f186'; }

        main {
            flex: 1;
            padding: 20px;
        }

        section {
            margin-bottom: 20px;
            background: white;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            padding: 20px;
            transition: all 0.3s ease;
        }

        .hidden {
            display: none;
            opacity: 0;
            transform: translateY(20px);
        }

        footer {
            background-color: var(--secondary-color);
            color: var(--text-color);
            text-align: center;
            padding: 10px;
            font-size: 0.9em;
        }

        footer a {
            color: var(--text-color);
            text-decoration: none;
        }

        #map {
            width: 100%;
            height: 600px;
            border-radius: 8px;
            overflow: hidden;
            position: relative;
        }

        .loading {
            font-style: italic;
            color: grey;
            text-align: center;
        }

        .star-rating {
            direction: rtl;
            unicode-bidi: bidi-override;
            display: inline-block;
        }
        .star-rating input[type="radio"] {
            display: none;
        }
        .star-rating label {
            display: inline-block;
            padding: 5px;
            cursor: pointer;
            color: #ccc;
            transition: color 0.3s;
        }
        .star-rating label:hover, .star-rating input[type="radio"]:checked ~ label {
            color: #FFD700;
        }

        .dark-mode {
            background-color: #121212;
            color: #ffffff;
        }
        .dark-mode header, .dark-mode footer {
            background-color: #222;
        }
        .dark-mode section {
            background-color: #333;
            color: #e0e0e0;
        }
        .dark-mode nav button, .dark-mode .star-rating label:hover, .dark-mode .star-rating input[type="radio"]:checked ~ label {
            background-color: #64DD17;
            color: #333;
        }

        .fade-in {
            animation: fadeInAnimation ease 0.5s;
            animation-iteration-count: 1;
            animation-fill-mode: forwards;
        }

        @keyframes fadeInAnimation {
            0% {
                opacity: 0;
                transform: translateY(20px);
            }
            100% {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .map-controls {
            position: absolute;
            bottom: 10px;
            left: 10px;
            display: flex;
        }

        .map-controls button, .map-controls input {
            margin-right: 5px;
            padding: 5px 10px;
            border-radius: 5px;
            border: none;
            background-color: rgba(255, 255, 255, 0.8);
        }

        .toast {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border-radius: 5px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
            animation: slideIn 0.5s ease;
        }

        @keyframes slideIn {
            from { transform: translateY(50px); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }

        nav button:focus, input:focus, select:focus {
            outline: 2px solid #2196F3;
            outline-offset: 2px;
        }

        .sighting-item {
            border: 1px solid #ddd;
            padding: 10px;
            margin-bottom: 10px;
            border-radius: 4px;
        }

        .graph {
            width: 100%;
            height: 300px;
            background: #f9f9f9;
            border-radius: 8px;
            margin-top: 20px;
        }

        .graph.dark-mode {
            background: #444;
        }
    </style>
</head>
<body>
    <header>
        <h1>SkyWatch</h1>
        <nav>
            <button id="trackBtn" aria-label="Track Aircraft">Track Aircraft</button>
            <button id="soundBtn" aria-label="Record Sound">Record Sound</button>
            <button id="imageBtn" aria-label="Analyze Image">Analyze Image</button>
            <button id="reportBtn" aria-label="Report Sighting">Report Sighting</button>
            <button id="identifyBtn" aria-label="Identify What You Saw">Identify What You Saw</button>
            <button id="learnBtn" aria-label="Learn About the Sky">Learn About the Sky</button>
            <button id="toggleTheme" aria-label="Toggle Dark Mode">Toggle Dark Mode</button>
        </nav>
    </header>

    <main>
        <section id="mapSection" class="hidden">
            <div id="map" class="loading">Loading map...</div>
            <div class="map-controls">
                <button id="zoomIn">+</button>
                <button id="zoomOut">-</button>
                <input type="search" id="mapSearch" placeholder="Search aircraft...">
            </div>
            <div id="aircraftDetails"></div>
        </section>
        <section id="soundSection" class="hidden">
            <button id="startRecord">Start Recording</button>
            <button id="stopRecord" class="hidden">Stop Recording</button>
            <div id="soundAnalysis"></div>
        </section>
        <section id="imageSection" class="hidden">
            <button id="selectImage">Select Image</button>
            <div id="imageAnalysis"></div>
        </section>
        <section id="reportSection" class="hidden">
            <h2>Report a Sighting</h2>
            <form id="sightingForm">
                <label for="objectDescription">Description:</label>
                <input type="text" id="objectDescription" name="description" required>

                <label for="length">Length (meters):</label>
                <input type="number" id="length" name="length" min="0">

                <label for="sound">Sound Description:</label>
                <input type="text" id="sound" name="sound">

                <label for="altitude">Altitude (meters):</label>
                <input type="number" id="altitude" name="altitude" min="0">

                <label for="location">Your Location:</label>
                <input type="text" id="location" name="location">

                <label for="time">Time of Sighting:</label>
                <input type="datetime-local" id="time" name="time">

                <label for="credibility">Credibility:</label>
                <div class="star-rating">
                    <input type="radio" id="star5" name="rating" value="5" /><label for="star5" title="5 stars">5</label>
                    <input type="radio" id="star4" name="rating" value="4" /><label for="star4" title="4 stars">4</label>
                    <input type="radio" id="star3" name="rating" value="3" /><label for="star3" title="3 stars">3</label>
                    <input type="radio" id="star2" name="rating" value="2" /><label for="star2" title="2 stars">2</label>
                    <input type="radio" id="star1" name="rating" value="1" /><label for="star1" title="1 star">1</label>
                </div>

                <button type="submit">Submit Sighting</button>
            </form>
            <div id="reportConfirmation"></div>
        </section>
        <section id="identifySection" class="hidden">
            <h2>Identify What You Saw</h2>
            <form id="identifyForm">
                <label for="shape">Shape:</label>
                <select id="shape" name="shape" required>
                    <option value="">Select Shape</option>
                    <option value="disk">Disk</option>
                    <option value="triangle">Triangle</option>
                    <option value="sphere">Sphere</option>
                    <option value="other">Other</option>
                </select>

                <label for="color">Color:</label>
                <input type="text" id="color" name="color" required>

                <label for="behavior">Behavior:</label>
                <input type="text" id="behavior" name="behavior" required>

                <button type="submit">Identify</button>
            </form>
            <div id="identificationResults"></div>
        </section>
        <section id="educationSection" class="hidden">
            <h2>Learn About the Sky</h2>
            <article>
                <h3>Identifying Planes vs. UFOs</h3>
                <p>Here's how you can tell the difference...</p>
            </article>
            <div class="graph" id="sightingsGraph"></div>
        </section>
        <section id="communitySection" class="hidden">
            <h2>Recent Sightings</h2>
            <div id="sightingsFeed">
                <!-- Mock sightings -->
                <div class="sighting-item">A disk-shaped object seen over LA</div>
                <div class="sighting-item">Triangle in the night sky, no sound</div>
            </div>
        </section>
    </main>

    <footer>
        <p>© 2025 SkyWatch | <a href="/privacy">Privacy Policy</a> | <a href="/terms">Terms of Service</a></p>
    </footer>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const buttons = {
                'trackBtn': 'mapSection',
                'soundBtn': 'soundSection',
                'imageBtn': 'imageSection',
                'reportBtn': 'reportSection',
                'identifyBtn': 'identifySection',
                'learnBtn': 'educationSection',
                'toggleTheme': 'communitySection'
            };

            Object.keys(buttons).forEach(btnId => {
                document.getElementById(btnId).addEventListener('click', () => showSection(buttons[btnId]));
            });

            document.getElementById('toggleTheme').addEventListener('click', () => {
                document.body.classList.toggle('dark-mode');
                // Now it only toggles the theme, not showing the community section
            });

            // Mock data for demonstration
            const mockAircrafts = [
                { id: "1", name: "Boeing 737", type: "Passenger", lat: 51.5074, lng: -0.1278 },
                { id: "2", name: "Airbus A320", type: "Passenger", lat: 51.4974, lng: -0.1178 }
            ];

            function showSection(sectionId) {
                hideAll();
                const section = document.getElementById(sectionId);
                section.classList.remove('hidden');
                section.classList.add('fade-in');
                if(sectionId === 'mapSection') initializeMap();
                else if(sectionId === 'soundSection') setupSoundControls();
                else if(sectionId === 'imageSection') setupImageControls();
                else if(sectionId === 'educationSection') initializeGraph();
            }

            function initializeMap() {
                document.getElementById('map').innerHTML = "Map would be here if we had real-time data.";
                mockAircrafts.forEach(aircraft => {
                    const marker = document.createElement('div');
                    marker.style.cssText = `
                        position: absolute;
                        top: 50%; left: 50%;
                        transform: translate(-50%, -50%);
                        width: 20px; height: 20px;
                        background-color: red;
                        border-radius: 50%;
                        cursor: pointer;
                        transition: transform 0.3s ease;
                    `;
                    marker.addEventListener('click', () => {
                        showAircraftDetails(aircraft);
                        marker.style.transform = 'translate(-50%, -50%) scale(1.2)';
                    });
                    marker.addEventListener('mouseout', () => {
                        marker.style.transform = 'translate(-50%, -50%)';
                    });
                    document.getElementById('map').appendChild(marker);
                });

                document.getElementById('zoomIn').addEventListener('click', () => {
                    showToast('Zooming in...');
                });
                document.getElementById('zoomOut').addEventListener('click', () => {
                    showToast('Zooming out...');
                });
                document.getElementById('mapSearch').addEventListener('input', () => {
                    showToast('Searching for ' + this.value);
                });
            }

            function showAircraftDetails(aircraft) {
                document.getElementById('aircraftDetails').innerHTML = `
                    <h3 style="margin-bottom: 10px;">${aircraft.name}</h3>
                    <p>Type: ${aircraft.type}</p>
                `;
            }

            function setupSoundControls() {
                const startRecord = document.getElementById('startRecord');
                const stopRecord = document.getElementById('stopRecord');
                const analysisDiv = document.getElementById('soundAnalysis');

                startRecord.addEventListener('click', () => {
                    startRecord.classList.add('hidden');
                    stopRecord.classList.remove('hidden');
                    analysisDiv.textContent = "Recording...";
                });

                stopRecord.addEventListener('click', () => {
                    stopRecord.classList.add('hidden');
                    startRecord.classList.remove('hidden');
                    analysisDiv.textContent = "Analyzing sound...";
                    setTimeout(() => {
                        analysisDiv.textContent = "Sound Analysis: Generic Aircraft Noise";
                    }, 2000);
                });
            }

            function setupImageControls() {
                const selectImageBtn = document.getElementById('selectImage');
                const analysisDiv = document.getElementById('imageAnalysis');

                selectImageBtn.addEventListener('click', () => {
                    analysisDiv.textContent = "Analyzing image...";
                    setTimeout(() => {
                        analysisDiv.textContent = "Image Analysis: Detected an Aircraft";
                    }, 2000);
                });
            }

            document.getElementById('sightingForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const formData = new FormData(this);
                const report = Object.fromEntries(formData.entries());
                
                if (!report.description) {
                    showToast('Description is required!');
                    return;
                }
                document.getElementById('reportConfirmation').textContent = 'Thank you for your report!';
                showToast('Sighting reported successfully!');
                console.log(report);
            });

            document.getElementById('identifyForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const formData = new FormData(this);
                const identificationQuery = Object.fromEntries(formData.entries());
                document.getElementById('identificationResults').textContent = "Identifying...";
                setTimeout(() => {
                    const results = mockIdentification(identificationQuery);
                    document.getElementById('identificationResults').innerHTML = `<p>Matches: ${results.join(', ')}</p>`;
                }, 2000);
                console.log(identificationQuery);
            });

            function mockIdentification(query) {
                if (query.shape === 'disk' && query.color === 'silver') {
                    return ['UFO', 'Drone'];
                } else if (query.shape === 'triangle') {
                    return ['F-117 Nighthawk', 'Stealth Drone'];
                }
                return ['Unknown'];
            }

            function hideAll() {
                document.querySelectorAll('section').forEach(section => {
                    section.classList.add('hidden');
                    section.classList.remove('fade-in');
                });
                document.getElementById('aircraftDetails').innerHTML = '';
                document.getElementById('soundAnalysis').textContent = '';
                document.getElementById('imageAnalysis').textContent = '';
                document.getElementById('reportConfirmation').textContent = '';
                document.getElementById('identificationResults').textContent = '';
            }

            function showToast(message) {
                const toast = document.createElement('div');
                toast.className = 'toast';
                toast.textContent = message;
                document.body.appendChild(toast);
                setTimeout(() => toast.remove(), 3000);
            }

            function initializeGraph() {
                const graph = document.getElementById('sightingsGraph');
                graph.innerHTML = "Graph would be here with real data.";
            }
        });
    </script>
</body>
</html>
