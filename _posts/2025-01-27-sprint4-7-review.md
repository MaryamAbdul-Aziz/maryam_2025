---
title: Sprints 4-7 (Bookworms) Blog Post
layout: post
courses: { compsci: {week: 20} }
type: tangibles
comments: true 
---

## Purpose

The purpose of our project was to create a hub for readers and book lovers to come together and discuss, review, and track books. We included features in our project such as user profiles, book suggestions, a random book recommender, and book review capabilities to serve our userbase.

### Purpose of my feature(s)

I had two main features within this project:
    - Fixing the login page so that users could customize their experience on the site and tailor their book reading and reviews to their own preferences
    - A page to suggest books that the user might find were lacking within our book database

Both of these features served to increase the customizability of our site and ensure that users could have the experience they wanted on the Bookworms

![submission form]({{site.baseurl}}/images/suggest1.png)
![book display]({{site.baseurl}}/images/suggest2.png)


## List Requests

Within my frontend, I have a function `fetchBooks()` which turns JSON into DOM. After the user submits a form containing information about the book they would like to submit, they receive an alert informing them that their request has been received and the book appears in a book list on the page.

```javascript
document.getElementById('book-form').addEventListener('submit', async function(event) {
        event.preventDefault();
        
        // assign user-inputted fields to corresponding variables
        const title = document.getElementById('title').value;
        const author = document.getElementById('author').value;
        const genre = document.getElementById('genre').value;
        const description = document.getElementById('description').value;
        const coverImageUrl = document.getElementById('cover_url').value;

        // create a variable that holds all the data for a particular book
        const bookData = {
            title: title,
            author: author,
            genre: genre,
            description: description,
            cover_url: coverImageUrl
        };
        // adds the book to the database in backend
        try {
            const response = await fetch(`${pythonURI}/api/suggest`, {  
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
            fetchBooks(); 
        } catch (error) {
            console.error('Error adding book to suggestions:', error);
            alert('Error adding book to suggestions: ' + error.message);
        }
    });
    // add the suggested books to a physical display on the frontend
    async function fetchBooks() {
        try {
            const response = await fetch(new URL(`${pythonURI}/api/suggest/book`), fetchOptions);
            if (!response.ok) {
                throw new Error('Failed to fetch books: ' + response.statusText);
            }

        const books = await response.json();

        const bookList = document.getElementById('book-list-content');
        if (books.length === 0) {
            bookList.innerHTML = '<p style="color: #000000">No books added yet. Fill out the form above to start adding your favorite books!</p>';
            return;
        }

        // render books
        bookList.innerHTML = books
    .map(
        book => `
        <div class="book">
            <h3>${book.title}</h3>
            <p><strong>Author:</strong> ${book.author}</p>
            <p><strong>Description:</strong> ${book.description}</p>
            <img src="${book.cover_url}" alt="Cover image of ${book.title}">
        </div>
    `
    )
    .join('');

    } catch (error) {
        console.error('Error fetching books:', error);
    }
}

document.addEventListener('DOMContentLoaded', () => {
    fetchBooks();
});
```

Within my backend, I have a method in `api/suggest.py` that uses a modified version of my usual POST request and a list to allow for multiple books to be sent at once through Postman.

This POST request takes JSON inputs of book title, author, genre, description, and cover image URL and returns a success message as well as adds the book to the database.

```python
# Endpoint to add multiple suggested books (Create)
@suggest_api.route('/bulk', methods=['POST'])
def add_books_bulk():
    # ask for JSON input
    data = request.json

    if not isinstance(data, list):
        return jsonify({'error': 'Expected a list of books'}), 400

    # read results as list
    results = []

    for book_data in data:
        title = book_data.get('title')
        author = book_data.get('author')
        genre = book_data.get('genre')
        description = book_data.get('description')
        cover_url = book_data.get('cover_url')

        try:
            # Create and add the suggested book
            suggested_book = SuggestedBook(title=title, author=author, genre=genre, description=description, cover_url=cover_url)
            suggested_book.create()
            results.append({'message': f'Book {title} added successfully to suggestions', 'title': title})
        except Exception as e:
            results.append({'error': f'Failed to add book {title}', 'message': str(e), 'title': title})

    return jsonify(results), 201
```

In `model/suggest.py` I used CRUD methods to keep the database table up-to-date and easily altered via Postman. 

```python
def create(self):
        db.session.add(self)
        db.session.commit()
```
```python
    def read(self):
        return {
            "id": self.id,
            "title": self.title,
            "author": self.author,
            "genre": self.genre,
            "description": self.description,
            "cover_url": self.cover_url
        }
```
```python
    def update(self):
        try:
            db.session.commit()
        except Exception as e:
            db.session.rollback()
            raise e
```
```python
    def delete(self):   
        try:
            db.session.delete(self)
            db.session.commit()
        except Exception as e:
            db.session.rollback()
        raise Exception(f"An error occurred while deleting the object: {str(e)}") from e
```

Additionally, `model/suggest.py` populates default data within the suggested books database.

```python
def initSuggest():
    with app.app_context():
        db.create_all()
        
    # tester data
    suggest_data = [
        SuggestedBook(title="The Raven Boys", author="Maggie Stiefvater", genre="Fantasy", description="A young adult fantasy novel about a girl from a family of clairvoyants, the boys she befriends, and how their lives are intertwined along their journey to wake a slumbering king.", cover_url="https://m.media-amazon.com/images/I/71s5v4HfFjL._AC_UF1000,1000_QL80_.jpg"),
        SuggestedBook(title="Catch-22", author="Joseph Heller", genre="Classics", description="The work centres on Captain John Yossarian, an American bombardier stationed on a Mediterranean island during World War II, and chronicles his desperate attempts to stay alive.", cover_url="https://d28hgpri8am2if.cloudfront.net/book_images/cvr9781451621174_9781451621174_hr.jpg")
    ]

    for suggestion in suggest_data:
            try:
                if not Book.query.filter_by(title=suggestion.title).first() and not SuggestedBook.query.filter_by(title=suggestion.title).first():
                    db.session.add(suggestion)
                    db.session.commit()
            except IntegrityError:
                # Fails with bad or duplicate data
                db.session.rollback()
```

## Sequencing, Selection, and Iteration

### Sequencing

When adding a book to the suggested books database, the code runs through a sequence:
1. Pull user-inputted data from the form in the frontend
2. Call the suggest API endpoint
3. JSONify the input
4. Add it to the database

### Selection

I use conditional statements throughout my code to ensure its functionality


This code checks to ensure the title is included when making a DELETE request

```python
@suggest_api.route('', methods=['POST'])  
def add_book():
    if not request.json or 'title' not in request.json:
        return jsonify({'error': 'Title is required to create the book'}), 400
```

This code checks to ensure there is book data in order to fetch a random book

```python
@suggest_api.route('/random', methods=['GET'])
def random_book():
    book = SuggestedBook.get_random_suggested_book()
    if book:
        return jsonify({
            'title': book.title,
            'author': book.author,
            'genre': book.genre,
            'description': book.description,
            'cover_url': book.cover_url
        })
    else:
        return jsonify({'error': 'No books found'}), 404
```

This code ensures that the title was inputted before updating a book

```python
@suggest_api.route('', methods=['PUT'])
def update_book():
    data = request.json

    title = data.get('title')
    if not title:
        return jsonify({'error': 'Title is required to update the book'}), 400
```

In my frontend, I have code that checks for existing suggested books when displaying suggested books.

```javascript
const bookList = document.getElementById('book-list-content');
        if (books.length === 0) {
            bookList.innerHTML = '<p style="color: #000000">No books added yet. Fill out the form above to start adding your favorite books!</p>';
            return;
        }
```

This code confirms the user wants to delete a book before deleting it.

```javascript
async function deleteBook(title) {
        if (confirm(`Are you sure you want to delete "${title}"?`)) {
    // more code here
}}
```

### Iteration

I use iteration for my bulk post book request.

```python
@suggest_api.route('/bulk', methods=['POST'])
def add_books_bulk():
    data = request.json

    if not isinstance(data, list):
        return jsonify({'error': 'Expected a list of books'}), 400

    results = []

    for book_data in data:
        title = book_data.get('title')
        author = book_data.get('author')
        genre = book_data.get('genre')
        description = book_data.get('description')
        cover_url = book_data.get('cover_url')
```

I also iterate through all books in the db when fetching all suggested books

```python
books_data = [
    {
        'title': book.title,
        'author': book.author,
        'genre': book.genre,
        'description': book.description,
        'cover_url': book.cover_url
    }
    for book in books
]
```


## Algorithmic Code Request

The methods within `model/suggest.py` are tied to functions within `api/suggest.py` that utilize POST (shown above), GET, PUT, and DELETE in Postman.

This GET request requires no JSON body input and outputs the title, author, genre, description, and cover image URL.

```python
# Endpoint to fetch a random suggested book (Read)
@suggest_api.route('/random', methods=['GET'])
def random_book():
    book = SuggestedBook.get_random_suggested_book()
    if book:
        return jsonify({
            'title': book.title,
            'author': book.author,
            'genre': book.genre,
            'description': book.description,
            'cover_url': book.cover_url
        })
    else:
        return jsonify({'error': 'No books found'}), 404
```

This PUT request takes JSON inputs of title and the field desired to be updated and outputs a success message as well as updates the book within the database. 

```python
# Endpoint to update existing suggested book (Update)
@suggest_api.route('', methods=['PUT'])
def update_book():
    data = request.json

    title = data.get('title')
    if not title:
        return jsonify({'error': 'Title is required to update the book'}), 400

    try:
        # Fetch the existing book by title
        suggested_book = SuggestedBook.query.filter_by(title=title).first()
        if not suggested_book:
            return jsonify({'error': 'Book not found'}), 404

        # Update the book details
        suggested_book.author = data.get('author', suggested_book.author)
        suggested_book.genre = data.get('genre', suggested_book.genre)
        suggested_book.description = data.get('description', suggested_book.description)
        suggested_book.cover_url = data.get('cover_url', suggested_book.cover_url)

        suggested_book.update() 
        return jsonify({'message': 'Book updated successfully'}), 200
    except Exception as e:
        return jsonify({'error': 'Failed to update book', 'message': str(e)}), 500
```

This DELETE request takes JSON inputs of title and outputs a success message as well as deletes the book from the backend database. 
```python
# Endpoint to delete suggested book (Delete)
@suggest_api.route('', methods=['DELETE'])
def delete_book():
    data = request.json
    
    title = data.get('title')
    if not title:
        return jsonify({'error': 'Title is required to delete the book'}), 400

    try:
        suggested_book = SuggestedBook.query.filter_by(title=title).first()
        
        if not suggested_book:
            return jsonify({'error': 'Book not found'}), 404
        
        suggested_book.delete()
        
        return jsonify({'message': 'Book deleted successfully'}), 200
    except Exception as e:
        return jsonify({'error': 'Failed to delete book', 'message': str(e)}), 500
```

## Call to Algorithm request

In order to create a suggested book, in my frontend, I use a function that calls my suggest books API endpoint and uses the POST feature.

```javascript
document.getElementById('book-form').addEventListener('submit', async function(event) {
        event.preventDefault();

        // define variables based on fields in form
        const title = document.getElementById('title').value;
        const author = document.getElementById('author').value;
        const genre = document.getElementById('genre').value;
        const description = document.getElementById('description').value;
        const coverImageUrl = document.getElementById('cover_url').value;

        const bookData = {
            title: title,
            author: author,
            genre: genre,
            description: description,
            cover_url: coverImageUrl
        };

        // post request
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

To delete a book, I call the suggest API endpoint and use the DELETE function.

```javascript
async function deleteBook(title) {
        if (confirm(`Are you sure you want to delete "${title}"?`)) {
            try { // delete request
                const response = await fetch(`${pythonURI}/api/suggest`, {
                    ...fetchOptions,
                    method: 'DELETE',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({ title }) // Pass title as an object
                });

                if (!response.ok) {
                    throw new Error('Failed to delete book: ' + response.statusText);
                }

                console.log("Book deleted successfully");
                alert('Book deleted successfully!');
                fetchBooks(); // Refresh the book list
            } catch (error) {
                console.error('Error deleting book:', error);
                alert('Error deleting book: ' + error.message);
            }
        } else {
            alert("Deletion canceled");
        }
    }
```

There is a similar function to update a book (PUT) that creates text fields for the user to update the book.

```javascript
async function updateBook(title) {
    // Find the book container based on the title
    const bookContainer = Array.from(document.querySelectorAll('.book'))
        .find(book => book.querySelector('h3').innerText === title);

    if (!bookContainer) {
        alert('Book not found for update.');
        return;
    }

    // Extract current book details
    const currentTitle = bookContainer.querySelector('h3').innerText;
    const currentAuthor = bookContainer.querySelector('p:nth-child(2)').innerText.split(': ')[1];
    const currentDescription = bookContainer.querySelector('p:nth-child(3)').innerText.split(': ')[1];
    const currentGenre = bookContainer.dataset.genre || '';
    const currentCoverUrl = bookContainer.querySelector('img').src;

    // creates editable fields
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

    // Cancel Button
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

To display all suggested books in the frontend, I use a fetch request to fetch the list of suggested books and then generate a div for the book. (see top)