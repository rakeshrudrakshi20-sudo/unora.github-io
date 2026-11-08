# unora.github-io

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>UNORA | Global Student Network</title>
<style>
*{box-sizing:border-box}
body{margin:0;font-family:Arial,sans-serif;background:#0b1020;color:#fff}
header{padding:18px 6%;display:flex;justify-content:space-between;
align-items:center;background:#111a30;border-bottom:1px solid #283451}
.logo{font-size:27px;font-weight:800;color:#a99bff;letter-spacing:2px}
button{background:#705cff;color:white;border:0;padding:11px 17px;
border-radius:9px;cursor:pointer;font-weight:bold}
button:hover{background:#5743e8}
input,select,textarea{width:100%;padding:12px;margin:7px 0;
border:1px solid #34415f;border-radius:9px;background:#151f36;color:white}
.container{max-width:1100px;margin:25px auto;padding:0 15px}
.layout{display:grid;grid-template-columns:220px 1fr;gap:20px}
.card{background:#151f36;padding:19px;border-radius:15px;
border:1px solid #283451;margin-bottom:17px}
nav button{display:block;width:100%;margin:9px 0;text-align:left;background:#202b46}
nav button.active{background:#705cff}
h2{color:#b9adff}
small,.muted{color:#a8b2ca}
.post{white-space:pre-wrap;line-height:1.5}
.hidden{display:none}
.row{display:flex;gap:10px;align-items:center;flex-wrap:wrap}
#feed{margin-top:15px}
@media(max-width:700px){
.layout{grid-template-columns:1fr}
nav{display:grid;grid-template-columns:1fr 1fr;gap:5px}
nav button{margin:3px 0}
}
</style>
</head>
<body>

<header>
 <div class="logo">✦ UNORA</div>
 <div class="muted">YOUR CAMPUS. YOUR WORLD.</div>
</header>

<div class="container">

<section id="auth" class="card">
 <h2>Welcome to UNORA</h2>
 <p class="muted">Connect with students. Share your world.</p>
 <input id="name" placeholder="Your full name">
 <input id="email" type="email" placeholder="Email address">
 <select id="school">
  <option value="">Select your school</option>
  <option>Engineering</option>
  <option>Management</option>
  <option>Applied Science</option>
  <option>Pharmacy</option>
  <option>Other / To be added</option>
 </select>
 <input id="course" placeholder="Course name">
 <select id="year">
  <option value="">Select academic year</option>
  <option>1st Year</option>
  <option>2nd Year</option>
  <option>3rd Year</option>
  <option>4th Year</option>
  <option>Other</option>
 </select>
 <button onclick="register()">Create demo account</button>
 <p class="muted">Demo only: do not enter sensitive personal information.</p>
</section>

<section id="app" class="hidden">
 <div class="layout">
  <nav class="card">
   <h2>UNORA</h2>
   <button onclick="showPage('home',this)" class="active">🏠 Home Feed</button>
   <button onclick="showPage('students',this)">👥 Students</button>
   <button onclick="showPage('confessions',this)">💭 Confessions</button>
   <button onclick="showPage('memories',this)">📸 Memories</button>
   <button onclick="logout()">↪ Logout</button>
  </nav>

  <main>
   <div id="home" class="page">
    <div class="card">
     <h2>What's happening?</h2>
     <textarea id="postText" placeholder="Share something with students..."></textarea>
     <button onclick="addPost()">Publish Post</button>
    </div>
    <div id="feed"></div>
   </div>

   <div id="students" class="page hidden">
    <div class="card">
     <h2>Student Community</h2>
     <p class="muted">Your demo profile appears here. Real global discovery needs a shared database.</p>
     <div id="studentList"></div>
    </div>
   </div>

   <div id="confessions" class="page hidden">
    <div class="card">
     <h2>Student Confessions</h2>
     <p class="muted">Share thoughts without displaying your name to other students.</p>
     <textarea id="confessionText" placeholder="Write your confession..."></textarea>
     <button onclick="addConfession()">Share Confession</button>
    </div>
    <div id="confessionList"></div>
   </div>

   <div id="memories" class="page hidden">
    <div class="card">
     <h2>College Time Capsule</h2>
     <input id="memoryYear" placeholder="Memory year (e.g. 2026)">
     <textarea id="memoryText" placeholder="Describe your college memory..."></textarea>
     <button onclick="addMemory()">Save Memory</button>
    </div>
    <div id="memoryList"></div>
   </div>
  </main>
 </div>
</section>
</div>

<script>
const get = key => JSON.parse(localStorage.getItem(key)||"[]");
const save = (key,data) => localStorage.setItem(key,JSON.stringify(data));
let me = JSON.parse(localStorage.getItem("unoraUser")||"null");

function register(){
 const user={
  name:document.getElementById("name").value.trim(),
  email:document.getElementById("email").value.trim(),
  school:document.getElementById("school").value,
  course:document.getElementById("course").value.trim(),
  year:document.getElementById("year").value
 };
 if(!user.name||!user.email||!user.school||!user.course||!user.year){
  alert("Please complete all fields.");return;
 }
 me=user;
 localStorage.setItem("unoraUser",JSON.stringify(me));
 openApp();
}
function openApp(){
 if(!me)return;
 document.getElementById("auth").classList.add("hidden");
 document.getElementById("app").classList.remove("hidden");
 render();
}
function logout(){
 me=null;
 localStorage.removeItem("unoraUser");
 location.reload();
}
function showPage(id,btn){
 document.querySelectorAll(".page").forEach(p=>p.classList.add("hidden"));
 document.getElementById(id).classList.remove("hidden");
 document.querySelectorAll("nav button").forEach(b=>b.classList.remove("active"));
 btn.classList.add("active");
}
function addPost(){
 const value=document.getElementById("postText").value.trim();
 if(!value)return;
 const posts=get("unoraPosts");
 posts.unshift({id:Date.now(),name:me.name,school:me.school,
 course:me.course,text:value,likes:0});
 save("unoraPosts",posts);
 document.getElementById("postText").value="";
 render();
}
function likePost(id){
 const posts=get("unoraPosts");
 const post=posts.find(p=>p.id===id);
 if(post)post.likes++;
 save("unoraPosts",posts);render();
}
function addConfession(){
 const value=document.getElementById("confessionText").value.trim();
 if(!value)return;
 const items=get("unoraConfessions");
 items.unshift({text:value,school:me.school});
 save("unoraConfessions",items);
 document.getElementById("confessionText").value="";
 render();
}
function addMemory(){
 const year=document.getElementById("memoryYear").value.trim();
 const text=document.getElementById("memoryText").value.trim();
 if(!year||!text){alert("Enter a year and memory.");return}
 const items=get("unoraMemories");
 items.unshift({year,text});
 save("unoraMemories",items);
 document.getElementById("memoryText").value="";
 render();
}
function render(){
 document.getElementById("feed").innerHTML=get("unoraPosts").map(p=>`
 <div class="card">
 <b>${escapeHTML(p.name)}</b>
 <p class="muted">${escapeHTML(p.school)} · ${escapeHTML(p.course)}</p>
 <div class="post">${escapeHTML(p.text)}</div>
 <button onclick="likePost(${p.id})">♥ ${p.likes} Likes</button>
 </div>`).join("")||'<p class="muted">No posts yet. Be the first to share!</p>';

 document.getElementById("studentList").innerHTML=`
 <div class="card">
 <b>${escapeHTML(me.name)}</b>
 <p>${escapeHTML(me.school)} · ${escapeHTML(me.course)}</p>
 <p class="muted">${escapeHTML(me.year)}</p>
 </div>`;

 document.getElementById("confessionList").innerHTML=get("unoraConfessions").map(c=>`
 <div class="card">
 <b>Anonymous Student</b>
 <p class="muted">${escapeHTML(c.school)}</p>
 <p>${escapeHTML(c.text)}</p>
 </div>`).join("");

 document.getElementById("memoryList").innerHTML=get("unoraMemories").map(m=>`
 <div class="card">
 <h2>${escapeHTML(m.year)}</h2>
 <p>${escapeHTML(m.text)}</p>
 </div>`).join("");
}
function escapeHTML(value){
 return String(value).replace(/[&<>"']/g,c=>({
 "&":"&amp;","<":"&lt;",">":"&gt;",
 '"':"&quot;","'":"&#39;"
 }[c]));
}
if(me)openApp();
</script>
</body>
</html>
