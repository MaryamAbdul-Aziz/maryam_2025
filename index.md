---
layout: base
title: Maryam's Home Page 
description: Home Page
hide: true
---

## This is the start of my AP Computer Science Principles journey here at Del Norte High School!

### I love to read! Here are some of my favorite books:

<style>
    .grid-container {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(150px, 1fr)); /* Dynamic columns */
        gap: 10px;
    }
    .grid-item {
        text-align: center;
    }
    .grid-item img {
        width: 100%;
        height: 200px; /* Fixed height for uniformity */
        max-height: 200px;
        object-fit: contain; /* Ensure the image fits within the fixed height */
    }
    .grid-item p {
        margin: 5px 0; /* Add some margin for spacing */
    }
</style>

<div class="grid-container" id="grid_container">
</div>

<script>
    var container = document.getElementById("grid_container"); // This container connects to the HTML div

    var http_source = "https://upload.wikimedia.org/wikipedia/en/";
    var books_read = [
        {cover: "d/dc/The_Hunger_Games.jpg", title: "The Hunger Games", word_count: "99,750 words"},
        {cover: "9/99/Lastolympian.gif", title: "The Last Olympian", word_count: "89,002 words"},
        {cover: "c/c4/DJMacHale_TheNeverWar.jpg", title: "The Never War", word_count: "117,136 words"},
        {cover: "a/a9/Winter_marissa_meyer_book_cover.jpg", title: "Winter", word_count: "206,750 words"},
        {cover: "e/ee/Tex_SE_Hinton.jpg", title: "Tex", word_count: "56,167 words"},
    ]; 
    

    for (const book of books_read) {
        var gridItem = document.createElement("div");
        gridItem.className = "grid-item";  // This class name connects the gridItem to the CSS style elements
        // Add "img" HTML tag for the cover
        var img = document.createElement("img");
        img.src = http_source + book.cover; // concatenate the source and cover
        img.alt = book.cover + "Cover"; 

        var title = document.createElement("p");
        title.textContent = book.title; // extract the description

        var word_count = document.createElement("p");
        word_count.textContent = book.word_count;  // extract the greeting

        // Append img and p HTML tags to the grid item DIV
        gridItem.appendChild(img);
        gridItem.appendChild(title);
        gridItem.appendChild(word_count);

        // Append the grid item DIV to the container DIV
        container.appendChild(gridItem);
    }
</script>

<br>
![code overlay](images/compsci.jpg)