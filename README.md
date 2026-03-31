<!DOCTYPE html>
<html>
<head>
<title>Railway Mutual Transfer Portal - Professional</title>
<style>
body{font-family:Arial;background:#eef2f7;margin:0}
.header{background:#2c7be5;color:#fff;padding:15px;text-align:center;font-size:20px}
.container{max-width:1200px;margin:20px auto;background:#fff;padding:20px;border-radius:10px}
.row{display:flex;flex-wrap:wrap;gap:10px}
input,select,textarea{padding:8px;width:100%;border-radius:6px;border:1px solid #ccc}
button, .edit-btn{padding:6px 12px;border:none;border-radius:6px;margin-top:5px;cursor:pointer}
.primary{background:#2c7be5;color:#fff}
.edit-btn{background:#ffc107;color:#000}
.edit-btn:hover{opacity:0.8}
.delete-btn{background:red;color:#fff}
.table{width:100%;margin-top:20px;border-collapse:collapse}
.table th,.table td{border:1px solid #ddd;padding:8px;text-align:center}
.table th{background:#2c7be5;color:#fff}
.match{background:#d4edda}
</style>
</head>
<body>

<div class="header">🚆 Railway Mutual Transfer Portal</div>
<div class="container">

<!-- PROFILE -->
<h3>👤 Create Profile</h3>
<div class="row">
<input id="name" placeholder="Name">
<input id="designation" placeholder="Designation">
<select id="medical">
<option value="">Medical Category</option>
<option>A1</option><option>A2</option><option>A3</option>
<option>B1</option><option>B2</option><option>B3</option>
<option>C1</option><option>C2</option>
</select>
<select id="department">
<option value="">Select Department</option>
<option>MECHANICAL</option><option>ELECTRICAL</option><option>ENGINEERING</option>
<option>SIGNAL & TELECOM</option><option>OPERATING</option><option>COMMERCIAL</option>
<option>STORE</option><option>STORES</option><option>TRAFFIC</option>
<option>ACCOUNTS</option><option>PERSONNEL</option><option>MEDICAL</option>
<option>SECURITY (RPF)</option><option>SAFETY</option><option>IT / COMPUTER</option>
<option>VIGILANCE</option><option>LEGAL</option><option>RAJBHASHA</option>
<option>PUBLIC RELATIONS</option><option>CATERING & TOURISM</option>
</select>
<select id="zone" onchange="loadDivisions()"></select>
<select id="division"><option>Select Division</option></select>
<input id="station" placeholder="Current Station">
<textarea id="remarks" placeholder="Remarks (Only Admin & Match can see)"></textarea>
</div>

<!-- REQUIREMENT -->
<h3>🎯 Add Requirement</h3>
<div class="row">
<select id="reqZone" onchange="loadReqDivisions()"></select>
<select id="reqDivision"><option>Select Division</option></select>
<input id="reqStation" placeholder="Desired Station">
</div>

<!-- OTP Verification BEFORE SAVE -->
<h3>📱 Mobile & OTP (Verify Before Saving)</h3>
<div class="row">
<input id="mobile" placeholder="Mobile Number">
<button class="primary" onclick="sendOTP()">Send OTP</button>
<input id="otp" placeholder="Enter OTP">
<button class="primary" onclick="verifyOTP()">Verify OTP</button>
</div>

<!-- SAVE BUTTON -->
<button id="saveBtn" class="primary" onclick="saveProfileAndRequirement()" disabled>Save Profile & Requirement</button>
<hr>

<!-- SEARCH -->
<h3>🔍 Search</h3>
<div class="row">
<input id="fName" placeholder="Name">
<input id="fDesignation" placeholder="Designation">
<select id="fMedical">
<option value="">Medical Category</option><option>A1</option><option>A2</option><option>A3</option>
<option>B1</option><option>B2</option><option>B3</option>
<option>C1</option><option>C2</option>
</select>
<select id="fDepartment">
<option value="">Department</option>
<option>MECHANICAL</option><option>ELECTRICAL</option><option>ENGINEERING</option>
<option>SIGNAL & TELECOM</option><option>OPERATING</option><option>COMMERCIAL</option>
<option>STORE</option><option>STORES</option><option>TRAFFIC</option>
<option>ACCOUNTS</option><option>PERSONNEL</option><option>MEDICAL</option>
<option>SECURITY (RPF)</option><option>SAFETY</option><option>IT / COMPUTER</option>
<option>VIGILANCE</option><option>LEGAL</option><option>RAJBHASHA</option>
<option>PUBLIC RELATIONS</option><option>CATERING & TOURISM</option>
</select>
<select id="fZone" onchange="loadFilterDivisions()"></select>
<select id="fDivision"><option>Select Division</option></select>
<input id="fStation" placeholder="Station">
</div>
<button class="primary" onclick="filterData()">Search</button>
<hr>

<h3>📊 Profiles & Mutual Matches</h3>
<table class="table" id="table"></table>

</div>

<script>
let users = [];
let verified = false;
let otpSent = false;
let currentOTP = "";
let isAdmin = false; // Change this to true for admin login
const divisionData = {
CR:["Bhusawal","Mumbai CR","Nagpur","Pune","Solapur"],
ER:["Asansol","Howrah","Malda","Sealdah"],
ECR:["Danapur","Dhanbad","Pt. Deen Dayal Upadhyaya","Samastipur","Sonpur"],
ECoR:["Khurda Road","Sambalpur","Waltair"],
NR:["Ambala","Delhi","Firozpur","Lucknow","Moradabad"],
NCR:["Agra","Jhansi","Prayagraj"],
NER:["Izzatnagar","Lucknow","Varanasi"],
NFR:["Alipurduar","Katihar","Lumding","Rangiya","Tinsukia"],
NWR:["Ajmer","Bikaner","Jaipur","Jodhpur"],
SR:["Chennai","Madurai","Palakkad","Salem","Thiruvananthapuram","Tiruchchirappalli"],
SCR:["Guntakal","Guntur","Hyderabad","Nanded","Secunderabad","Vijayawada"],
SER:["Adra","Chakradharpur","Kharagpur","Ranchi"],
SECR:["Bilaspur","Nagpur","Raipur"],
SWR:["Bengaluru","Hubballi","Mysuru"],
WR:["Ahmedabad","Bhavnagar","Mumbai WR","Rajkot","Ratlam","Vadodara"],
WCR:["Bhopal","Jabalpur","Kota"],
SCoR:["Guntakal","Guntur","Vijayawada","Waltair"],
KR:["Karwar","Ratnagiri"],
Metro:["Kolkata Metro"]
};

// Load zones
function loadZones(){
let zones=Object.keys(divisionData);
zone.innerHTML=reqZone.innerHTML=fZone.innerHTML='<option>Select Zone</option>';
zones.forEach(z=>{
zone.innerHTML+=`<option>${z}</option>`;
reqZone.innerHTML+=`<option>${z}</option>`;
fZone.innerHTML+=`<option>${z}</option>`;
});
}
loadZones();

function loadDivisions(){division.innerHTML='<option>Select Division</option>'; (divisionData[zone.value]||[]).forEach(d=>division.innerHTML+=`<option>${d}</option>`);}
function loadReqDivisions(){reqDivision.innerHTML='<option>Select Division</option>'; (divisionData[reqZone.value]||[]).forEach(d=>reqDivision.innerHTML+=`<option>${d}</option>`);}
function loadFilterDivisions(){fDivision.innerHTML='<option>Select Division</option>'; (divisionData[fZone.value]||[]).forEach(d=>fDivision.innerHTML+=`<option>${d}</option>`);}

// OTP
function sendOTP(){
if(!mobile.value) return alert("Enter mobile number first!");
otpSent = true;
currentOTP = Math.floor(1000+Math.random()*9000).toString();
alert("✅ OTP sent to "+mobile.value+" (OTP: "+currentOTP+")");
}
function verifyOTP(){
if(!otpSent) return alert("Please click 'Send OTP' first!");
if(otp.value === currentOTP){
verified = true;
alert("✅ OTP Verified. Save button enabled.");
document.getElementById("saveBtn").disabled=false;
} else {
alert("❌ Invalid OTP");
}
}

// Mask mobile
function maskMobile(m){return m?m.slice(0,2)+"XXXXXX"+m.slice(-2):""}

// Save
function saveProfileAndRequirement(){
if(!verified) return alert("Please verify OTP first! 📱");
if(!name.value || !department.value || !zone.value) return alert("Please fill Name, Department, and Zone before saving.");

users.push({
name:name.value,
department:department.value,
designation:designation.value,
medical:medical.value,
zone:zone.value,
division:division.value,
station:station.value,
remarks:remarks.value,
mobile:mobile.value,
reqZone:reqZone.value,
reqDivision:reqDivision.value,
reqStation:reqStation.value
});

// Clear
name.value=department.value=designation.value=medical.value=zone.value=division.value=station.value=remarks.value=reqZone.value=reqDivision.value=reqStation.value=mobile.value=otp.value="";
verified=false; otpSent=false; currentOTP="";
document.getElementById("saveBtn").disabled=true;

loadTable();
alert("✅ Profile & Requirement saved successfully!");
}

// Mutual match
function findMatch(u){return users.filter(x=>x!==u && x.zone===u.reqZone && u.zone===x.reqZone);}

// Edit profile
function editProfile(mob){
let user = users.find(u=>u.mobile===mob);
if(!user) return;
if(!isAdmin && user.mobile !== mobile.value) return alert("You can only edit your own profile.");
name.value=user.name; designation.value=user.designation; medical.value=user.medical;
department.value=user.department; zone.value=user.zone; loadDivisions(); division.value=user.division;
station.value=user.station; remarks.value=user.remarks;
reqZone.value=user.reqZone; loadReqDivisions(); reqDivision.value=user.reqDivision; reqStation.value=user.reqStation;
mobile.value=user.mobile; verified=true;
document.getElementById("saveBtn").disabled=false;
}

// Delete profile
function deleteProfile(mob){
if(!isAdmin) return alert("Only Admin can delete!");
if(confirm("⚠️ Are you sure you want to delete this profile?")){
users = users.filter(u=>u.mobile!==mob);
loadTable();
alert("✅ Profile deleted successfully!");
}
}

// Load table
function loadTable(){
table.innerHTML=`<tr>
<th>Name</th><th>Dept</th><th>Zone</th><th>Match</th>
<th>Partners</th><th>Remarks</th><th>Edit</th><th>Delete</th>
</tr>`;
users.forEach(u=>{
let m=findMatch(u);
let partners=m.length?m.map(p=>p.name+" ("+maskMobile(p.mobile)+")").join("<br>"):"No Match";
let showRemarks=isAdmin||m.length>0;
let canEdit = isAdmin || (u.mobile===mobile.value);
let canDelete = isAdmin;
table.innerHTML+=`<tr class="${m.length?'match':''}">
<td>${u.name}</td>
<td>${u.department}</td>
<td>${u.zone}</td>
<td>${m.length}</td>
<td>${partners}</td>
<td>${showRemarks?u.remarks:"Hidden"}</td>
<td>${canEdit?'<span class="edit-btn" onclick="editProfile(\''+u.mobile+'\')">Edit</span>':''}</td>
<td>${canDelete?'<span class="edit-btn delete-btn" onclick="deleteProfile(\''+u.mobile+'\')">Delete</span>':''}</td>
</tr>`;
});
}

// Filter/Search
function filterData(){
let result=users.filter(u=>
(u.name||"").toLowerCase().includes(fName.value.toLowerCase()) &&
(u.designation||"").toLowerCase().includes(fDesignation.value.toLowerCase()) &&
(u.medical||"").toLowerCase().includes(fMedical.value.toLowerCase()) &&
(u.department||"").toLowerCase().includes(fDepartment.value.toLowerCase()) &&
(u.zone||"").toLowerCase().includes(fZone.value.toLowerCase()) &&
(u.division||"").toLowerCase().includes(fDivision.value.toLowerCase()) &&
(u.station||"").toLowerCase().includes(fStation.value.toLowerCase())
);
loadTable(result);
}
</script>

</body>
</html>
