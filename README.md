<html>
<head>
    <title>Tracking Link Generator</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css"></link>
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Roboto', sans-serif;
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen flex flex-col items-center">
    <header class="bg-blue-600 w-full py-4 shadow-md">
        <div class="container mx-auto px-4">
            <h1 class="text-white text-3xl font-bold">Tracking Link Generator</h1>
        </div>
    </header>
    <main class="container mx-auto px-4 py-8 flex-grow">
        <div class="bg-white p-6 rounded-lg shadow-lg">
            <h2 class="text-2xl font-bold mb-4">Create a Tracking Link</h2>
            <form id="linkForm" class="space-y-4">
                <div>
                    <label for="url" class="block text-sm font-medium text-gray-700">URL to Track</label>
                    <input type="url" id="url" name="url" required class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm">
                </div>
                <div>
                    <label for="description" class="block text-sm font-medium text-gray-700">Description</label>
                    <input type="text" id="description" name="description" required class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm">
                </div>
                <button type="submit" class="w-full bg-blue-600 text-white py-2 px-4 rounded-md shadow-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50">Generate Link</button>
            </form>
        </div>
        <div class="bg-white p-6 rounded-lg shadow-lg mt-8">
            <h2 class="text-2xl font-bold mb-4">Generated Links</h2>
            <table class="min-w-full divide-y divide-gray-200">
                <thead class="bg-gray-50">
                    <tr>
                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Description</th>
                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Tracking Link</th>
                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Actions</th>
                    </tr>
                </thead>
                <tbody id="linkTableBody" class="bg-white divide-y divide-gray-200">
                    <!-- Generated links will appear here -->
                </tbody>
            </table>
        </div>
        <div class="bg-white p-6 rounded-lg shadow-lg mt-8">
            <h2 class="text-2xl font-bold mb-4">Tracked Locations</h2>
            <table class="min-w-full divide-y divide-gray-200">
                <thead class="bg-gray-50">
                    <tr>
                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Description</th>
                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">IP Address</th>
                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Location</th>
                        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Timestamp</th>
                    </tr>
                </thead>
                <tbody id="locationTableBody" class="bg-white divide-y divide-gray-200">
                    <!-- Tracked locations will appear here -->
                </tbody>
            </table>
        </div>
    </main>
    <footer class="bg-gray-800 w-full py-4">
        <div class="container mx-auto px-4 text-center text-white">
            &copy; 2023 Tracking Link Generator. All rights reserved.
        </div>
    </footer>
    <script>
        document.getElementById('linkForm').addEventListener('submit', function(event) {
            event.preventDefault();
            const url = document.getElementById('url').value;
            const description = document.getElementById('description').value;
            const linkTableBody = document.getElementById('linkTableBody');

            const trackingUrl = `http://localhost:3000/track?url=${encodeURIComponent(url)}&description=${encodeURIComponent(description)}`;

            const newRow = document.createElement('tr');
            newRow.innerHTML = `
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${description}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-blue-600"><a href="${trackingUrl}" target="_blank">${trackingUrl}</a></td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                    <button class="text-red-600 hover:text-red-900" onclick="deleteRow(this)">Delete</button>
                </td>
            `;
            linkTableBody.appendChild(newRow);

            document.getElementById('linkForm').reset();
        });

        function deleteRow(button) {
            const row = button.closest('tr');
            row.remove();
        }

        async function fetchLocations() {
            const response = await fetch('http://localhost:3000/locations');
            const locations = await response.json();
            const locationTableBody = document.getElementById('locationTableBody');

            locations.forEach(location => {
                const newRow = document.createElement('tr');
                newRow.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${location.description}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">${location.ip}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">${location.location.city}, ${location.location.region}, ${location.location.country_name}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">${new Date(location.timestamp).toLocaleString()}</td>
                `;
                locationTableBody.appendChild(newRow);
            });
        }

        fetchLocations();
    </script>
</body>
</html>
