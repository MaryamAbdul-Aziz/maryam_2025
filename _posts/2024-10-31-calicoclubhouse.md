---
permalink: /voteforthegoat/calicoclubhouse/
layout: post
title: Calico Clubhouse
description: Welcome to the DNHS Calico Critters Club!
---

<style>
.header-text {
    font-size: 40px;
    font-family: "Times New Roman", Times, serif;
    text-align: center;
}
.button-text {
    background-color: #d14dc0;
    cursor: pointer;
}
.button-image {
    border: none;
    width: 50px;
    height: 50px;
    cursor: pointer;
}
.container {
    display: flex;
    align-items: center;
    justify-content: space-between; /* Push content to opposite sides */
    width: 100%;
    padding: 10px;
    box-sizing: border-box;
}
.button-container {
    display: flex;
    flex-direction: column;
    gap: 20px;
}
.header-image-container {
    width: 30%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center; 
    gap: 10px;    
}
.header-image-container img {
    width: 100%;
    height: auto;
    object-fit: contain;
}
.image-container {
    width: auto;
    display: flex;
    flex-direction: row;
    justify-content: center; 
    gap: 10px;    
    margin: 0 auto;
}
.image-container img {
    width: auto;
    height: auto;
    max-height: 250px;
    cursor: pointer;
}

/* post styles */
.pfp {
    display: flex;
    align-items: center;
    margin-bottom: 10px; 
}
.pfp img {
    width: auto;
    max-height: 50px;
    max-width: 50px;
    margin-right: 10px;
    object-fit: contain;
}
.username {
    font-size: 24px;
}
.post {
    align-items: center;
    font-size: 20px;
    /*
    background-color: #2b2d42;
    border: 1px solid #ddd;
    max-width: 600px;
    padding: 15px;
    margin: 20px auto;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    border-radius: 5px;
    */
    
}
hr {
    background-color: #ddd;
    height: 1px;
    border: none; 
    margin: 30px 0; 
    background: linear-gradient(to right, transparent, #5f0f40, transparent);
}
.post-footer {
    display: flex;
    justify-content: flex-end;
    font-size: 18px;
    padding: 10px;
    justify-content: space-between;
    gap: 10px;
}
.heart {
    margin-left: auto;
    font-size: 22px;
}
.like-container {
    margin-left: auto;
    order: 2;
    display: flex;
    align-items: center;
}
.commentbutton {
    margin-right: auto;
    order: 1;
}
.comments-section {
    display: none; 
    max-width: 600px; 
    margin: 0 auto; 
    padding: 10px;
}
</style>

<div>
<img src="{{site.baseurl}}/images/calicocritters/heading.png" alt="Calico Critters Clubhouse header">
</div>

<div class="container">
<div class="button-container" class="header-text">
    <a href="https://www.example.com" target="_blank">
        <button class="button-text">Members</button>
    </a>
    <a href="https://www.example.com/" target="_blank">
        <button class="button-text">Collections available</button>
    </a>
</div>

<div class="header-image-container">
    <img src="{{site.baseurl}}/images/calicocritters/mizuki.png" alt="Sample Critter">
    <a href="https://www.example.com" target="_blank">
        <button class="button-text">Re-generate!</button>
    </a>
</div>
</div>
<br>
<div>
<p class="header-text">Click to enter! ↓</p>

<div class="image-container">
    <a href="https://www.example.com" target="_blank">
        <img src="{{site.baseurl}}/images/calicocritters/mizukihouse.png" alt="Adventure Tree House">
    </a>
    <a href="https://www.example.com" target="_blank">
        <img src="{{site.baseurl}}/images/calicocritters/noryhouse.png" alt="Spooky Surprise House">
    </a>
    <a href="https://www.example.com" target="_blank">
        <img src="{{site.baseurl}}/images/calicocritters/emihouse.png" alt="Sylvanian Families Restaurant House">
    </a>
</div>
<br>
<br>
<!--posts-->
<h3 style="text-align: center;">Timeline</h3>
<div class="post" style="align-items: center; background-color: #E48A9C; border: 1px solid #ddd; max-width: 600px; padding: 15px; margin: 20px auto; border-radius: 5px;">
    <div class="pfp">
        <img src="{{site.baseurl}}/images/logo.png">
        <span class="username">CalicoCritterLover236</span>
    </div>
    <hr>
    <div>
        <p>I love love Calico Critters! I have 236 of them!</p>
    </div>
    <hr>
    <div class="post-footer">
    <div class="like-container">
        <a href="javascript:void(0);" onclick="likeFunction(this)">
            <span class="like">🤍</span>
        </a>
        </div>
    <div class="commentbutton-container">
        <button id="commentButton" class="button-text" onclick="toggleCommentSection();">Comments</button>
    </div>
</div>


<div id="commentSection" style="display: none; max-width: 600px; margin: 20px auto; padding: 10px; border: 1px solid #ddd; border-radius: 5px;">
    <input type="text" id="usernameInput" placeholder="Enter your username" style="width: 80%; padding: 8px; margin-bottom: 5px;">
    <input type="text" id="commentInput" placeholder="Enter your comment" style="width: 80%; padding: 8px;">
    <button onclick="addComment();" class="button-text" style="padding: 8px; margin-top: 5px;">Submit</button>
    <div id="commentList" style="margin-top: 10px;"></div>
</div>


<script>
function likeFunction(element){
    const heart = element.querySelector('.like');
        heart.textContent = heart.textContent === '🤍' ? '❤️' : '🤍';
}

// Function to toggle the visibility of the comment section
function toggleCommentSection() {
    const commentSection = document.getElementById('commentSection');
    commentSection.style.display = 
        commentSection.style.display === 'none' ? 'block' : 'none';
}

// Function to add a new comment and store it in local storage
function addComment() {
    const usernameInput = document.getElementById('usernameInput');
    const commentInput = document.getElementById('commentInput');

    // Ensure both username and comment are filled in
    if (usernameInput.value.trim() === "" || commentInput.value.trim() === "") {
        alert("Please enter both a username and a comment.");
        return;
    }

    // Get existing comments from local storage, or initialize as an empty array
    let comments = JSON.parse(localStorage.getItem('comments')) || [];

    // Add the new comment as an object with username and text
    const newComment = {
        username: usernameInput.value.trim(),
        text: commentInput.value.trim(),
    };

    comments.push(newComment);
    localStorage.setItem('comments', JSON.stringify(comments)); // Save to local storage

    // Clear the input fields
    usernameInput.value = '';
    commentInput.value = '';

    // Refresh the displayed comments
    displayComments();
}

// Function to display all comments from local storage
function displayComments() {
    const commentList = document.getElementById('commentList');
    commentList.innerHTML = ''; // Clear existing comments

    let comments = JSON.parse(localStorage.getItem('comments')) || [];

    // Create a comment element for each stored comment
    comments.forEach(comment => {
        const commentItem = document.createElement('div');
        commentItem.style.marginBottom = '10px';
        
        const usernameElement = document.createElement('strong');
        usernameElement.textContent = `${comment.username}: `;
        
        const textElement = document.createElement('span');
        textElement.textContent = comment.text;

        commentItem.appendChild(usernameElement);
        commentItem.appendChild(textElement);
        commentList.appendChild(commentItem);
    });
}

// Display comments on page load
window.onload = displayComments;
</script>