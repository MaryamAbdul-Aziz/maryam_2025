---
toc: true
layout: post
title: Night at the Museum Retrospective (Final Exam)
type: tangibles
courses: { compsci: {week: 24} }
---

# 5 things completed over 12 weeks

[Project Kanban Board/Burndown](https://github.com/users/gabrielac07/projects/2/views/1)

[Team Issue](https://github.com/nighthawkcoders/portfolio_2025/issues/621#issuecomment-2552405468)

[Individual Issue](https://github.com/MaryamAbdul-Aziz/maryam_2025/issues/8)


1. Learned how to create API endpoints with HTTP methods like POST, GET, PUT, DELETE

2. Created frontend display of suggested books using fetch requests that call APIs to use HTTP methods like update and delete to edit user-inputted info

3. Created read, update, restore, backup methods in backend that help build table/model in database and ensure data can be backed up and restored after data is initialized.

4. Created endpoint that "accepts" or "rejects" user-suggested books, requiring user authentication/token and using an endpoint that adds the book to the main database and deletes from the suggested database while telling the user which books have been reviewed by admin.

5. Guided deployment within my group, creating deployment blog, registering DNS name using Route53 in AWS, setting up Certbot to ensure site is secured and using https, and pulling in the backend when changes are made to update deployment (docker-compose down, build, up, etc.)


# Full Stack Project, CPT Requirements

INPUT: My code receives input from the user when:
    - Submitting a suggestion
    - Updating a suggestion
    - Deleting a suggestion
    - Accepting a suggestion
    - Rejecting a suggestion

```javascript
document.getElementById('book-form').addEventListener('submit', async function(event) {
        event.preventDefault();

        const title = document.getElementById('title').value;
        const author = document.getElementById('author').value;
        const genre = document.getElementById('genre').value;
        const description = document.getElementById('description').value;
        const cover_url = document.getElementById('cover_url').value;

        const bookData = {
            title: title,
            author: author,
            genre: genre,
            description: description,
            cover_url: cover_url
        };
        
        try {
            const response = await fetch(`${pythonURI}/api/suggest`, {  // Use /api/suggest endpoint
                ...fetchOptions,
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(bookData)
            });

            if (!response.ok) {
                throw new Error('Failed to add book to suggestions: ' + response.statusText);
            }

            const result = await response.json();
            console.log("Book added to suggestions successfully")
            alert('Book added successfully!');
            document.getElementById('book-form').reset();
            fetchBooks();  // Refresh book list
        } catch (error) {
            console.error('Error adding book to suggestions:', error);
            alert('Error adding book to suggestions: ' + error.message);
        }
    });
```

Updating a suggestion:
```javascript
async function updateBook(title) {
    const bookContainer = Array.from(document.querySelectorAll('.book'))
        .find(book => book.querySelector('h3').innerText === title);

    if (!bookContainer) {
        alert('Book not found for update.');
        return;
    }

    const currentTitle = bookContainer.querySelector('h3').innerText;
    const currentAuthor = bookContainer.querySelector('p:nth-child(2)').innerText.split(': ')[1];
    const descriptionElement = Array.from(bookContainer.querySelectorAll('p'))
        .find(p => p.innerText.startsWith('Description:'));
    const currentDescription = descriptionElement ? descriptionElement.innerText.replace('Description: ', '') : '';
    const currentGenre = bookContainer.dataset.genre || '';
    const currentCoverUrl = bookContainer.querySelector('img').src;

    // Replace static fields with editable inputs
    bookContainer.innerHTML = `
        <input type="text" id="edit-title" value="${currentTitle}" placeholder="Title">
        <input type="text" id="edit-author" value="${currentAuthor}" placeholder="Author">
        <select id="edit-genre">
            <option value="Classics" ${currentGenre === 'Classics' ? 'selected' : ''}>Classics</option>
            <option value="Fantasy" ${currentGenre === 'Fantasy' ? 'selected' : ''}>Fantasy</option>
            <option value="Nonfiction" ${currentGenre === 'Nonfiction' ? 'selected' : ''}>Nonfiction</option>
            <option value="Historical Fiction" ${currentGenre === 'Historical Fiction' ? 'selected' : ''}>Historical Fiction</option>
            <option value="Suspense/Thriller" ${currentGenre === 'Suspense/Thriller' ? 'selected' : ''}>Suspense/Thriller</option>
            <option value="Romance" ${currentGenre === 'Romance' ? 'selected' : ''}>Romance</option>
            <option value="Dystopian" ${currentGenre === 'Dystopian' ? 'selected' : ''}>Dystopian</option>
            <option value="Mystery" ${currentGenre === 'Mystery' ? 'selected' : ''}>Mystery</option>
        </select>
        <textarea id="edit-description" placeholder="Description">${currentDescription}</textarea>
        <input type="text" id="edit-cover-url" value="${currentCoverUrl}" placeholder="Cover URL">
        <button id="save-update">OK</button>
        <button id="cancel-update">Cancel</button>
    `;

    // Handle Cancel Button
    document.getElementById('cancel-update').addEventListener('click', () => {
        fetchBooks(); // Reload the book list to cancel editing
    });

    document.getElementById('save-update').addEventListener('click', async () => {
        const updatedTitle = document.getElementById('edit-title').value;
        const updatedAuthor = document.getElementById('edit-author').value;
        const updatedGenre = document.getElementById('edit-genre').value;
        const updatedDescription = document.getElementById('edit-description').value;
        const updatedCoverUrl = document.getElementById('edit-cover-url').value;

        const updatedBookData = {
            title: title,
            author: updatedAuthor,
            genre: updatedGenre,
            description: updatedDescription,
            cover_url: updatedCoverUrl
        };

        try {
            const response = await fetch(`${pythonURI}/api/suggest`, {
                ...fetchOptions,
                method: 'PUT',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(updatedBookData)
            });

            if (!response.ok) {
                throw new Error('Failed to update book: ' + response.statusText);
            }

            console.log('Book updated successfully');
            alert('Book updated successfully!');
            fetchBooks(); // Refresh the book list
        } catch (error) {
            console.error('Error updating book:', error);
            alert('Error updating book: ' + error.message);
        }
    });
}
```

Deleting a suggestion:
```javascript
let acceptedBooks = ""; // Variable to store accepted books list

    function updateAcceptedBooksList() {
        const acceptedBooksListElement = document.getElementById("accepted-books-list");
        acceptedBooksListElement.textContent = acceptedBooks.trim() ? acceptedBooks : "None yet.";
    }

    async function acceptBook(title) {
    const bookContainer = Array.from(document.querySelectorAll('.book'))
        .find(book => book.querySelector('h3').innerText === title);

    if (!bookContainer) {
        alert('Book not found for acceptance.');
        return;
    }

    const author = bookContainer.querySelector('p:nth-child(2)').innerText.split(': ')[1];
    const genre = bookContainer.querySelector('p:nth-child(3)').innerText.split(': ')[1];
    const description = bookContainer.querySelector('p:nth-child(4)').innerText.replace('Description: ', '');
    const cover_url = bookContainer.querySelector('img').src;

    const bookData = {
        title: title,
        author: author,
        genre: genre,
        description: description,
        cover_url: cover_url
    };

    try {
        const response = await fetch(`${pythonURI}/api/suggest/accept`, { 
            ...fetchOptions,
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(bookData)
        });

        const response2 = await fetch(`${pythonURI}/api/suggest`, {
            ...fetchOptions,
            method: 'DELETE',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({ title }) 
        });

        if (!response2.ok) {
            throw new Error('Failed to accept book: ' + response2.statusText);
        }

        acceptedBooks += (acceptedBooks ? ", " : "") + title;
        updateAcceptedBooksList(); // Update display
        
        console.log("Book accepted successfully");
        alert('Book accepted successfully!');
        console.log("Book removed from suggestions successfully");
        fetchBooks();  // Refresh book list
    } catch (error) {
        console.error('Error accepting book:', error);
        alert('Error accepting book: ' + error.message);
    }
}    
```

Rejecting a suggestion:
```javascript
let rejectedBooks = ""; // Variable to store accepted books list

    function updateRejectedBooksList() {
        const rejectedBooksListElement = document.getElementById("rejected-books-list");
        rejectedBooksListElement.textContent = rejectedBooks.trim() ? rejectedBooks : "None yet.";
    }

async function rejectBook(title) {
    if (confirm(`Are you sure you want to reject "${title}"?`)) {
        try {
            const response = await fetch(`${pythonURI}/api/suggest`, {
                ...fetchOptions,
                method: 'DELETE',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ title }) // Pass title as an object
            });

            if (!response.ok) {
                throw new Error('Failed to reject book: ' + response.statusText);
            }

            console.log("Book rejected successfully");
            alert('Book rejected successfully!');
            rejectedBooks += (rejectedBooks ? ", " : "") + title;
            updateRejectedBooksList();
            fetchBooks();
        } catch (error) {
            console.error('Error rejecting book:', error);
            alert('Error rejecting book: ' + error.message);
        }
    } else {
        alert("Rejection canceled");
    }
}
```

LIST: I used a list to initialize my suggest book data.

```python
suggest_data = [
        SuggestedBook(title="The Raven Boys", author="Maggie Stiefvater", genre="Fantasy", description="A young adult fantasy novel about a girl from a family of clairvoyants, the boys she befriends, and how their lives are intertwined along their journey to wake a slumbering king.", cover_url="https://m.media-amazon.com/images/I/71s5v4HfFjL._AC_UF1000,1000_QL80_.jpg"),
        SuggestedBook(title="Catch-22", author="Joseph Heller", genre="Classics", description="The work centres on Captain John Yossarian, an American bombardier stationed on a Mediterranean island during World War II, and chronicles his desperate attempts to stay alive.", cover_url="https://d28hgpri8am2if.cloudfront.net/book_images/cvr9781451621174_9781451621174_hr.jpg"),
        SuggestedBook(title="A Midsummer Night\'s Dream", author="William Shakespeare", genre="Classics", description="Four Athenians run away to the forest only to have Puck the fairy make both of the boys fall in love with the same girl.", cover_url="https://m.media-amazon.com/images/I/71plvG7VRiL._AC_UF1000,1000_QL80_.jpg"),
        SuggestedBook(title="Never Let Me Go", author="Kazuo Ishiguro", genre="Mystery", description="Never Let Me Go follows students\' lives at an elite boarding school. The story explores themes of friendship, memories, and what it means to be human, gradually revealing deeper mysteries about the nature of their world.", cover_url="https://images.penguinrandomhouse.com/cover/9781400078776"),        
        SuggestedBook(title="A Deadly Education", author="Naomi Novik", genre="Fantasy", description="A Deadly Education is a 2020 fantasy novel written by American author Naomi Novik following Galadriel \"El\" Higgins, a half-Welsh, half-Indian sorceress, who must survive to graduation while controlling her destructive abilities at a school of magic very loosely inspired by the legend of the Scholomance.", cover_url="https://m.media-amazon.com/images/I/81j2VmcrS-L._AC_UF1000,1000_QL80_.jpg"),
        SuggestedBook(title="House of Leaves", author="Mark Z. Danielewski", genre="Suspense/Thriller", description="The House of Leaves synopsis details a story about a young man who finds a manuscript about a family's documentary, The Navidson Record, which details their experiences with a strange house.", cover_url="https://images.penguinrandomhouse.com/cover/9780375420528"),
        SuggestedBook(title="The Cruel Prince", author="Holly Black", genre="Fantasy", description="Jude was seven years old when her parents were murdered and she and her two sisters were stolen away to live in the treacherous High Court of Faerie. Ten years later, Jude wants nothing more than to belong there, despite her mortality. But many of the fey despise humans.", cover_url="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRFRlLnHzV7FMzb6ouRYUgQmKd34ss7NZTbcw&s"),    
        SuggestedBook(title="The Very Hungry Caterpillar", author="Eric Carle", genre="Classics", description="The plot follows a very hungry caterpillar that consumes a variety of foods before pupating and becoming a butterfly", cover_url="https://upload.wikimedia.org/wikipedia/en/b/b5/HungryCaterpillar.JPG"),    
    ]
```

PROCEDURE: The restore procedure outlines how to re

# N@tM Feedback

# Project Feature write up

5 points - 5 things you did over 12 weeks, Issues, burndown, presentation

2 points - Full Stack Project Demo, including CPT requirement highlights, and N@tM feedback

1 point - Project Feature blog write up, using CPT/FRQ language

1 point - MCQ