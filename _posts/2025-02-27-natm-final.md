---
toc: true
layout: post
title: Night at the Museum Retrospective (Final Exam)
type: tangibles
courses: { compsci: {week: 24} }
---

# Self-Grading

| Category | Grade | Points | Explanation |
|---|---|---|---|
| 5 things in 12 weeks | 5 | 5 | I believe I earned a full score in this category because of my hard work over the course of the trimester. Not only did I learn a lot over the course of these weeks about APIs, frontend to backend integration, collaboration, and deployment, I also achieved a significant amount of what I set out to do, set and met reasonable goals, and asked for help when needed. |
| Full Stack Project Demo (CPT Requirements, N@tM) | 1.85 | 2 | I know my code very well and can explain it using proper vocabulary, so I can give an in-depth demo of my feature. My code also fulfills all of the CPT requirements thoroughly and in a solid manner, often fulfilling requirements multiple times over. I  find that my code demonstrates a solid knowledge of CollegeBoard's cirriculum. My N@tM experience was also very positive, and I was able to talk to several people about my project and receive feedback from them. However, I find there is always room for improvement, and if I were to do this project over, I would find more and more inventive ways to implement CB requirements. |
| Project Feature blog Write-up | 1 | 1 | My write-up is thorough and explains both how my feature fulfills CPT requirements and is useful to my group's project overall. |
| MCQ | 0.95 | 1 | Although I did not earn a full score in my MCQ, I performed better than my last MCQ test and overall felt like I understood more. I also took the time afterward to take a look at my analytics and take note on what questions I missed, and I wrote a detailed reflection afterward. |
| Total | 8.8 | 9 | |

10th point:
- DMed teacher in advance of live review
- Attended N@tM, took interest in other groups, gave feedback and specific praise

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

## INPUT: 

My code receives input from the user when:
- Submitting a suggestion
- Updating a suggestion
- Deleting a suggestion
- Accepting a suggestion
- Rejecting a suggestion

### Submitting a suggestion:
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

### Updating a suggestion:
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

### Deleting a suggestion:
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

### Rejecting a suggestion:
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

## LIST: 
I used a list to initialize my suggest book data.

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

## PROCEDURE: 
The add suggested book procedure includes a descriptive name (`add_book()`), a return type of JSON and includes parameters of book information like `title`, `author`, `genre`, `description` and `cover_url`.

```python
# Endpoint to add suggested books (Create)
@suggest_api.route('', methods=['POST'])  
def add_book():
    if not request.json or 'title' not in request.json:
        return jsonify({'error': 'Title is required to create the book'}), 400
    data = request.json

    title = data.get('title')
    author = data.get('author')
    genre = data.get('genre')
    description = data.get('description')
    cover_url = data.get('cover_url')

    try:
        # Create and add the suggested book
        suggested_book = SuggestedBook(title=title, author=author, genre=genre, description=description, cover_url=cover_url)
        suggested_book.create()
        return jsonify({'message': 'Book added successfully to suggestions'}), 201
    except Exception as e:
        return jsonify({'error': 'Failed to add book', 'message': str(e)}), 500
```

## SEQUENCING, SELECTION, ITERATION: 
My `add_books_bulk()` function uses selection and iteration to add multiple books at once to the suggest database for user convenience

```python
@suggest_api.route('/bulk', methods=['POST'])
def add_books_bulk():
    data = request.json  
    if not isinstance(data, list):  # selection
        return jsonify({'error': 'Expected a list of books'}), 400

    results = []

    for book_data in data:  # iteration
        title = book_data.get('title')
        author = book_data.get('author')
        genre = book_data.get('genre')
        description = book_data.get('description')
        cover_url = book_data.get('cover_url')

        try:
            suggested_book = SuggestedBook(
                title=title, 
                author=author, 
                genre=genre, 
                description=description, 
                cover_url=cover_url
            )
            suggested_book.create() 

            results.append({'message': f'Book "{title}" added successfully to suggestions', 'title': title})  
        except Exception as e:
            results.append({'error': f'Failed to add book "{title}"', 'message': str(e), 'title': title})

    return jsonify(results), 201 
```

## OUTPUT: 
My code outputs both textual and visual information. It generates success and error messages for adding books to the database and also visually displays the books that were added to the database in the frontend.

Below is an example of one of many success/error messages within my code:
```python
return jsonify({'message': 'Book added successfully to suggestions'}), 201
    except Exception as e:
        return jsonify({'error': 'Failed to add book', 'message': str(e)}), 500
```

Visual display in frontend:
```javascript
async function fetchBooks() {
        try {
            const response = await fetch(new URL(`${pythonURI}/api/suggest/book`), fetchOptions); // Fetch all suggested books
            if (!response.ok) {
                throw new Error('Failed to fetch books: ' + response.statusText);
            }

        const books = await response.json();

        const bookList = document.getElementById('book-list-content');
        if (books.length === 0) {
            bookList.innerHTML = '<p style="color: #000000">No books added yet. Fill out the form above to start adding your favorite books!</p>';
            return;
        }

        // Render books
        bookList.innerHTML = books
    .map(
        book => `
        <div class="book">
            <h3>${book.title}</h3>
            <p><strong>Author:</strong> ${book.author}</p>
            <p><strong>Genre:</strong> ${book.genre}</p>
            <p><strong>Description:</strong> ${book.description}</p>
            <img src="${book.cover_url}" alt="Cover image of ${book.title}">
            <div class="book-options">
                <button class="updateButton" data-title="${book.title}">Update</button>
                <button class="deleteButton" data-title="${book.title}">Delete</button>
                <button class="acceptButton" data-title="${book.title}">Accept</button>
                <button class="rejectButton" data-title="${book.title}">Reject</button>
            </div>
        </div>
    `
    )
    .join('');
        
        document.querySelectorAll('.updateButton').forEach(button => {
            button.addEventListener('click', (event) => {
                const title = event.target.dataset.title; // Get the title from data attribute
                updateBook(title);
            });
        });
        // [...] cut for length
}}
```


# N@tM Feedback + Experience

I received valuable feedback at Night at the Museum, including a suggestion to filter books alphabetically for user convenience. 

I implemented the use of a simple selector and a conditional to allow users to sort their suggestions:
```html
<label for="sort-books">Sort Books:</label>
    <select id="sort-books">
        <option value="none">No Sort (Oldest > Newest)</option>
        <option value="alphabetical">Alphabetically</option>
    </select>

<script>
const sortOption = document.getElementById('sort-books').value;
    if (sortOption === "alphabetical") {
        books.sort((a, b) => a.title.localeCompare(b.title));
    }
</script>
```
<hr>
At Night at the Museum, I toured some of the work of other groups in CSP, asked thoughtful and relevant questions, and provided feedback where necessary. 

Some projects, such as the travelor project, really impressed me with their use of a real-time API that updated their page with stats on temperature, humidity, and more.
![travelor group]({{site.baseurl}}/images/natm-t2-1.JPG)

Others, like the restaurant group, had a wide range of data in their features like recipes filtered both by country and by type, which I really appreciated as user-friendly and optimal.
![restaurant group]({{site.baseurl}}/images/natm-t2-2.JPG)

# Project Feature Write-up

My feature for our project was a **suggestion feature, which allowed users in our book review website to suggest books they would like to see added to the main book database.** This included the use of **HTTP methods such as POST, GET, PUT, and DELETE** so users could suggest the books they wanted to see, view a full list of the books that they had suggested, update any mistakes, and delete incorrectly added books. Through admin implementation, **admins of the website can approve or reject books,** which are then subsequently added into the larger book database. This larger databse is used with other features such as the random book generator, reading list, cart, and comment feature. 

My project fulfills CPT requirements through use of **selection and iteration when adding books to the database.** When user fills out the form to add a book to the suggestions database, my code uses a **for loop to iterate through different key values** such as title, author, and genre to ensure that all the proper fields are included. Additionally, my code includes **both inputs and outputs.** The user inputs a book they would like to see suggested, and the code outputs both a success or error message, and a display of the books that have been suggested by that user.

From a user standpoint, my feature is useful and has purpose when considered in conjunction with the other features. Our existing book database is limited and does not include every book; therefore, if a user would like to review, recommend, comment on, buy, or otherwise discuss a book not in our database, they are at an impasse. **My feature bridges this gap by allowing users to suggest a book they would like to see and potentially add it to the larger database, enabling them to use it in the above capacities.**

# MCQ
Score: 60/67 (89.5%)

## Areas where I missed points:

- 1, Design and evaluate computational solutions for a purpose (14/17)
- 2, Develop and implement algorithms (11/13)
- 4, Evaluate and test algorithms and programs (10/12)

## Topic Skill Performance Errors:

- 3.6 Conditionals: 0% (0/1)
- 3.11 Binary Search: 0% (0/1)
- 3.5: Boolean Expressions: 50% (1/2)
- 3.17: Algorithmic Efficiency: 50% (1/2)
- 1.4: Identifying and Correcting Errors: 71% (5/7)
- 2.1: Binary Numbers: 80% (4/5)

## Corrections:

4. Cause of overflow error
- I conflated round-off errors with overflow errors

16. Error in wordList traversal
- I decreased the index only when the target words were found and not when an element is checked

42. Determine if score is within ten points of target
- I got confused reading the Boolean statements and mixed up target and score

47. Requirements for binary search
- I did not know binary searches needed sorted lists to work properly

50. Reasonable time algorithms
- Even though I tried searching for the definition of reasonable time, I still misunderstood that reasonable time *can* be represented by constants and polynomials

62. Which selection statements will display true
- I misunderstood the question and did not realize that since x OR y was equal to true, true would be displayed

67. Error in numOccurrences procedure
- I did not realize I was meant to select 2 answers for this question, and I did not realize that since the target word appeared in the middle of the list, it would incorrectly reset the count to 0.

# Reflection


Over the course of this trimester, I feel that I have learned a lot about computer science, including **APIs and HTTP methods, frontend to backend, collaboration, project management**, and more. Even though I do not plan to pursue a career in computer science, **I know that these skills will help me in the professional world**. 

Knowing what works and what does not, like **using Slack** to maintain communication between group members and **utilizing my debugging tools** as much as possible, I hope to carry these skills onto the next trimester with my next group. 

I have also learned a lot about myself and my own strengths and weaknesses. I think I have a strong suit for **problem solving**, including debugging, trying unique solutions, working around an issue, and **asking for help**, communicating my needs well. 

One of my weaknesses is **project synergy** with the rest of my group; while we communicate a lot about our issues and what needs to get done, many times, our project **felt out of sync with itself** as different parts all worked on their own like free agents. In the future, I will try to **ensure unity within my group** so that the finished project looks cohesive. 


<button onclick="toTop()" id="topBtn" title="Go to top">Top</button>

<style>
#topBtn {
  display: none; 
  position: fixed;
  bottom: 20px;
  right: 30px; 
  z-index: 99; 
  border: none; 
  outline: none; 
  background-color: #5C4877; 
  color: white;
  cursor: pointer; 
  padding: 15px; 
  border-radius: 10px; 
  font-size: 18px; 
}
</style>

<script>
let mybutton = document.getElementById("topBtn");

window.onscroll = function() {scrollFunction()};

function scrollFunction() {
  if (document.body.scrollTop > 20 || document.documentElement.scrollTop > 20) {
    mybutton.style.display = "block";
  } else {
    mybutton.style.display = "none";
  }
}

function toTop() {
  document.body.scrollTop = 0;
  document.documentElement.scrollTop = 0;
}
</script>