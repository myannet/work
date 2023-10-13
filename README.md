<html>
<head>
    <title>My Blog</title>
    <style>
        /* Add your CSS styles here */
        body {
            font-family: Arial, sans-serif;
            background-color: lightblue;
            margin: 0;
            padding: 0;
        }

        .container {
            max-width: 100%;
            margin: 0 auto;
            padding: 20px;
        }

        .blog-post {
            background-color: lightyellow;
            border: 1px solid #eee;
            padding: 15px;
            margin-bottom: 20px;
        }

        .blog-post h2 {
            font-size: 24px;
            margin: 0;
        }

        .blog-post .post-meta {
            font-style: italic;
        }

        .blog-post .post-content {
            margin-top: 10px;
        }

        .btn {
            display: inline-block;
            padding: 10px 20px;
            border: none;
            cursor: pointer;
        }
h1 {background-color: #a20200; color: yellow; 
text-align: center; padding: 20px; margin: 0; border-radius: 50px;
font-size: 50px;}
h3 {background-color: blue; color: white; text-align: center; padding: 20px;
margin: 0; border-radius: 50px; text-align: center;}
    </style>
</head>
<body>
    <div class="container">
        <h1>General Notes</h1>

        <h3>Localstorage ဖြင့် ဖန်တီးထားသည့်အတွက် ကိုယ်ရေးထည့်လိုက်သည့်မှတ်စုသည် အခြား devices များမှ ဝင်ကြည့်ရှု၍ မရနိုင်ပါ။</h3>
        <button id="new-post-button" class="btn">New Post</button>
        <button id="edit-post-button" class="btn">Edit Post</button>

        <form id="post-form" style="display: none;">
            <label for="title">Title:</label>
            <input type="text" id="title" required>
            <label for="author">Author:</label>
            <input type="text" id="author" required>
            <label for="content">Content:</label>
            <textarea id="content" required></textarea>
            <button type="submit" class="btn">Save Post</button>
        </form>

        <div id="blog-posts">
        </div>
    </div>

    <script>
        const postForm = document.getElementById("post-form");
        const newPostButton = document.getElementById("new-post-button");
        const editPostButton = document.getElementById("edit-post-button");
        const blogPostsContainer = document.getElementById("blog-posts");

        function displayBlogPosts() {
            blogPostsContainer.innerHTML = ''; // Clear existing posts
            const storedPosts = Object.keys(localStorage);

            if (storedPosts.length > 0) {
                storedPosts.forEach((key) => {
                    if (key !== 'blogPost') {
                        const post = JSON.parse(localStorage.getItem(key));
                        const postDiv = document.createElement("div");
                        postDiv.classList.add("blog-post");
                        postDiv.innerHTML = `
                            <h2>${post.title}</h2>
                            <p class="post-meta">Published on ${new Date().toDateString()} by ${post.author}</p>
                            <p class="post-content">${post.body}</p>
                            <button class="btn remove-post" data-key="${key}">Remove</button>
                            <button class="btn edit-post" data-key="${key}">Edit</button>
                        `;
                        blogPostsContainer.appendChild(postDiv);
                    }
                });
            }
        }

        function removeBlogPost(key) {
            localStorage.removeItem(key);
            displayBlogPosts();
        }

        function editBlogPost(key) {
            const post = JSON.parse(localStorage.getItem(key));
            document.getElementById("title").value = post.title;
            document.getElementById("author").value = post.author;
            document.getElementById("content").value = post.body;
            editPostButton.style.display = "block";
            postForm.dataset.editKey = key;
        }

        newPostButton.addEventListener("click", function () {
            postForm.style.display = "block";
            postForm.reset();
            editPostButton.style.display = "none";
        });

        editPostButton.addEventListener("click", function () {
            const editKey = postForm.dataset.editKey;
            if (editKey) {
                const title = document.getElementById("title").value;
                const author = document.getElementById("author").value;
                const content = document.getElementById("content").value;
                const post = { title, author, body: content };
                localStorage.setItem(editKey, JSON.stringify(post));
                displayBlogPosts();
                postForm.style.display = "none";
                editPostButton.style.display = "block";
                delete postForm.dataset.editKey;
            } else {
                alert("No post selected for editing.");
            }
        });

        const postFormSubmit = document.getElementById("post-form");
        postFormSubmit.addEventListener("submit", function (e) {
            e.preventDefault();
            const title = document.getElementById("title").value;
            const author = document.getElementById("author").value;
            const content = document.getElementById("content").value;
            const post = { title, author, body: content };
            const timestamp = new Date().getTime();
            localStorage.setItem(`post-${timestamp}`, JSON.stringify(post));
            displayBlogPosts();
            postForm.style.display = "none";
            editPostButton.style.display = "block";
        });

        // Event delegation for removing and editing posts
        blogPostsContainer.addEventListener("click", function (event) {
            if (event.target.classList.contains("remove-post")) {
                removeBlogPost(event.target.getAttribute("data-key"));
            }
            if (event.target.classList.contains("edit-post")) {
                editBlogPost(event.target.getAttribute("data-key"));
            }
        });

        // Initial display (load and display all stored posts)
        displayBlogPosts();
    </script>
</body>
</html>
