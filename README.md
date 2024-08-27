<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>V-Tech Creations</title>
    <style>
        /* Existing styles */
        body {
            background-color: #121212;
            color: #ffffff;
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
        }
        header {
            text-align: center;
            padding: 50px;
            background-image: url('hacker-background.jpg');
            background-size: cover;
        }
        header h1 {
            font-size: 3em;
            margin: 0;
        }
        header p {
            font-size: 1.2em;
            margin-top: 10px;
        }
        .container {
            padding: 20px;
            max-width: 800px;
            margin: auto;
        }
        form {
            background-color: #1e1e1e;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
        }
        form input, form textarea {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border-radius: 5px;
            border: none;
            background-color: #333;
            color: #fff;
        }
        form button {
            padding: 10px 20px;
            border-radius: 5px;
            border: none;
            background-color: #ff9800;
            color: #fff;
            cursor: pointer;
        }
        .message {
            text-align: center;
            background-color: #4caf50;
            padding: 20px;
            border-radius: 10px;
            display: none;
            margin-bottom: 10px;
        }
        .view-button {
            text-align: center;
            margin-bottom: 20px;
        }
        .view-button button {
            padding: 10px 20px;
            border-radius: 5px;
            border: none;
            background-color: #ff9800;
            color: #fff;
            cursor: pointer;
        }
        .ideas {
            background-color: #1e1e1e;
            padding: 20px;
            border-radius: 10px;
        }
        .idea {
            background-color: #2c2c2c;
            padding: 15px;
            border-radius: 5px;
            margin-bottom: 10px;
        }
        .idea h3 {
            margin: 0;
            margin-bottom: 5px;
        }
        .idea img {
            max-width: 100%;
            height: auto;
            border-radius: 5px;
            margin-top: 10px;
        }
        .stats {
            font-size: 0.9em;
            color: #ccc;
        }
        .interactions {
            margin-top: 10px;
        }
        .interactions button {
            padding: 5px 10px;
            border-radius: 5px;
            border: none;
            background-color: #ff9800;
            color: #fff;
            cursor: pointer;
            margin-right: 10px;
        }
        .interactions textarea {
            width: 100%;
            padding: 10px;
            border-radius: 5px;
            border: none;
            background-color: #333;
            color: #fff;
        }
    </style>
</head>
<body>

<header>
    <h1>V-tech Innovations</h1>
    <p>Welcome to V-tech... If you are an emerging innovator, share your idea with other innovators.Together lets us create the mesmerising and problem solving innovations the world has never seen.I am Vignesh an emerging innivator like you. I am looking for alternate and sustainable solutions to take my country India in the first place in developing technology.You could even buy electronics components for cheap from this web.Only if you all visit my web regularly! Welcome Again ! </p>
</header>

<div class="container">
    <form id="ideaForm">
        <input type="text" id="name" name="name" placeholder="Your Name" required>
        <input type="text" id="ideaName" name="ideaName" placeholder="Name of Your Idea" required>
        <input type="email" id="email" name="email" placeholder="Your Gmail Address" required>
        <textarea id="ideaDescription" name="ideaDescription" placeholder="Describe Your Idea" rows="5" required></textarea>
        <input type="file" id="circuitImage" name="circuitImage" accept="image/*,text/plain">
        <button type="submit">Submit</button>
    </form>

    <div id="message" class="message">Congratulations! Your idea has been submitted.</div>
    <div class="view-button">
        <button onclick="scrollToIdeas()">View Others' Ideas</button>
    </div>

    <div class="ideas" id="ideasContainer">
        <h2>Here are other innovators' ideas</h2>
        <!-- Submitted ideas will appear here -->
    </div>
</div>

<script>
    let ideas = JSON.parse(localStorage.getItem('ideas')) || [];
    let userId = localStorage.getItem('userId') || Date.now(); // Unique user ID for tracking

    document.getElementById('ideaForm').addEventListener('submit', function(event) {
        event.preventDefault();

        const email = document.getElementById('email').value;

        if (!isValidEmail(email)) {
            alert('Please use a valid Gmail address.');
            return;
        }

        document.getElementById('message').style.display = 'block';

        const name = document.getElementById('name').value;
        const ideaName = document.getElementById('ideaName').value;
        const ideaDescription = document.getElementById('ideaDescription').value;
        const circuitImage = document.getElementById('circuitImage').files[0];
        const ideaId = Date.now(); // Unique ID for the idea

        let imageHTML = '';
        if (circuitImage) {
            const imageURL = URL.createObjectURL(circuitImage);
            imageHTML = `<img src="${imageURL}" alt="Circuit Image">`;
        }

        const idea = {
            id: ideaId,
            name,
            ideaName,
            ideaDescription,
            imageHTML,
            views: 0,
            likes: 0,
            suggestions: [],
            likedBy: [],
            viewedBy: []
        };

        ideas.push(idea);
        saveIdeas();
        renderIdeas();

        document.getElementById('ideaForm').reset();
    });

    function isValidEmail(email) {
        return email.endsWith('@gmail.com');
    }

    function saveIdeas() {
        localStorage.setItem('ideas', JSON.stringify(ideas));
        // TODO: Implement Google Drive API to save ideas permanently
    }

    function renderIdeas() {
        const container = document.getElementById('ideasContainer');
        container.innerHTML = '<h2>Here are other innovators\' ideas</h2>';
        ideas.forEach(idea => {
            const ideaElement = document.createElement('div');
            ideaElement.classList.add('idea');
            ideaElement.innerHTML = `
                <h3>${idea.name} - ${idea.ideaName}</h3>
                <p>${idea.ideaDescription}</p>
                ${idea.imageHTML}
                <div class="stats">
                    <span>Visits: ${idea.views}</span> | <span>Likes: ${idea.likes}</span>
                </div>
                <div class="interactions">
                    <button onclick="incrementLikes(${idea.id}, this)">Like</button>
                    <button onclick="visitIdea(${idea.id}, this)">Visit</button>
                    <button onclick="viewIdeaDetails(${idea.id})">More Info</button>
                    <button onclick="deleteIdea(${idea.id})">Delete</button>
                </div>
            `;
            container.appendChild(ideaElement);
        });
    }

    function incrementLikes(ideaId, button) {
        const idea = ideas.find(i => i.id === ideaId);

        if (!idea.likedBy.includes(userId)) {
            idea.likes += 1;
            idea.likedBy.push(userId);
            saveIdeas();
            renderIdeas();
        } else {
            alert('You have already liked this idea.');
        }
    }

    function visitIdea(ideaId, button) {
        const idea = ideas.find(i => i.id === ideaId);

        if (!idea.viewedBy.includes(userId)) {
            idea.views += 1;
            idea.viewedBy.push(userId);
            saveIdeas();
            renderIdeas();
        } else {
            alert('You have already visited this idea.');
        }
    }

    function viewIdeaDetails(ideaId) {
        const idea = ideas.find(i => i.id === ideaId);
        const container = document.getElementById('ideasContainer');
        container.innerHTML = `
            <h2>${idea.name} - ${idea.ideaName}</h2>
            <p>${idea.ideaDescription}</p>
            ${idea.imageHTML}
            <div class="stats">
                <span>Visits: ${idea.views}</span> | <span>Likes: ${idea.likes}</span>
            </div>
            <div class="interactions">
                <button onclick="incrementLikes(${idea.id}, this)">Like</button>
                <textarea placeholder="Write your suggestion..." id="suggestionInput"></textarea>
                <button onclick="submitSuggestion(${idea.id})">Submit Suggestion</button>
            </div>
            <div id="suggestionsContainer">
                <h3>Suggestions</h3>
                <ul>
                    ${idea.suggestions.map(s => `<li>${s}</li>`).join('')}
                </ul>
            </div>
            <button onclick="renderIdeas()">Back to Ideas</button>
        `;
    }

    function submitSuggestion(ideaId) {
        const suggestionInput = document.getElementById('suggestionInput');
        const suggestion = suggestionInput.value.trim();
        if (suggestion) {
            const idea = ideas.find(i => i.id === ideaId);
            idea.suggestions.push(suggestion);
            saveIdeas();
            viewIdeaDetails(ideaId);
        }
    }

    function deleteIdea(ideaId) {
        if (confirm('Are you sure you want to delete this idea?')) {
            ideas = ideas.filter(i => i.id !== ideaId);
            saveIdeas();
            renderIdeas();
        }
    }

    function scrollToIdeas() {
        renderIdeas();
        document.getElementById('ideasContainer').scrollIntoView({ behavior: 'smooth' });
    }

    // Initialize the page with stored ideas
    renderIdeas();
</script>

</body>
</html>