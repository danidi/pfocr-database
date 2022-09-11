---
layout: table-page
order: 1
display-title: "Browse"
btn-class: "btn-front"
---
<div class="container">
<a class="btn btn-sm btn-front my-2" style="float:right;" href="/browse/table.html">Table</a>
  <div class="row">
    <div class="col-sm" id="checkboxes" style="max-width:300px;">
      <section class="facet">
      <div class="facet-header">
        <button type="button" class="btn btn-link facet-toggle" data-toggle="collapse" href="#organisms" aria-expanded="true" aria-controls="collapseExample">
        <strong class="facet-title">Organism</strong>
        </button>
      </div>
      <div class="facet-body collapse show" id="organisms">
        <ul class="facet-group">
          {% for org in site.organisms %}
            <li class="facet-group-item">
              <label class="form-check-label" title="{{org.latin}}">
              <input class="form-check-input" type="checkbox" name="organisms" value="{{ org.latin }}">
              {{ org.common }}
              </label>
            </li>
          {% endfor %}
        </ul>
      </div>
      <div class="facet-header">
        <button type="button" class="btn btn-link facet-toggle" data-toggle="collapse" href="#communities" aria-expanded="true" aria-controls="collapseExample">
        <strong class="facet-title">Community</strong>
        </button>
      </div>
      <div class="facet-body collapse" id="communities">
        <ul class="facet-group">
          {% for com in site.communities %}
            <li class="facet-group-item">
              <label class="form-check-label">
              <input class="form-check-input" type="checkbox" name="communities" value="{{ com.community-tag }}">
              {{ com.display-name }}
              </label>
            </li>
          {% endfor %}
        </ul>
      </div>
      {% assign type-group = site.annotations | group_by: "type" | reverse %}
      {% for type in type-group %}
        <div class="facet-header">
          <button type="button" class="btn btn-link facet-toggle" data-toggle="collapse" href="#{{ type.name |downcase | replace:" ","_"}}" aria-expanded="true" aria-controls="collapseExample">
          <strong class="facet-title">{{ type.name }}</strong>
          </button>
        </div>
        <div class="facet-body collapse" id="{{ type.name |downcase | replace:" ","_"}}">
          <ul class="facet-group">
            {% for annot in type.items %}
              <li class="facet-group-item">
                <label class="form-check-label">
                <input class="form-check-input" type="checkbox" name="{{ type.name |downcase | replace:" ","_"}}" value="{{ annot.value }}">
                {{ annot.value }}
                </label>
              </li>
            {% endfor %}
          </ul>
        </div>
      {% endfor %}
      </section>
    </div> <!-- End checkboxes div -->
    <div class="col-sm" id="tabs">
      <ul class="nav nav-tabs" style="float:left; margin-left: 0px;">
        <li class="nav-item">
          <a class="nav-link active" data-toggle="tab" href="#gallery">Gallery</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" data-toggle="tab" href="#list">List</a>
        </li>
      </ul>
      <div id="sortable" style="margin-left:300px;"> 
      <button type="button" class="btn btn-sm btn-outline-secondary" onclick="sortTable(6)" title="Sort by title">Title <i class="fa fa-sort"></i></button>
      <button type="button" class="btn btn-sm btn-outline-secondary" onclick="sortTable(5)" title="Sort by ID">ID <i class="fa fa-sort"></i></button>
      <button type="button" class="btn btn-sm btn-outline-secondary" onclick="sortTable(9)" title="Sort by last edited date">Date <i class="fa fa-sort"></i></button>
      </div>
      <div class="tab-content"  style="clear:both;">
        <div class="tab-pane fade show active" id="gallery" role="tabpanel">
          <br/>
          <div class="row" id="pwcards" style="margin-right: 0px; margin-left: 0px;">
            <!-- Add cards here -->
          </div>
          <br/>
          <input type="button" class="badge badge-secondary" id="cardsMore" value="show more" style="">
        </div>
        <div class="tab-pane fade" id="list" role="tabpanel">
          <br/>
          <ul id="pwlist" style="margin-left: 0px; list-style-type: none">
            <!-- Add list here -->
          </ul>
        </div>
      </div> 
    </div> <!-- End tabs -->
  </div> <!-- End row -->
  <div class="col-sm" id="hidden-table">
    <div class="row">
      <div class="results">
      </div>
      <div class="table-responsive-sm">
        <table  class="table table-sm" id="myTable"> 
            <th style="display:none;">Organism<br /><input type="text" id="org" style="width:50px;" onkeyup="filterTable()"></th>
            <th style="display:none;">Communities<br /><input type="text" id="com" style="width:50px;" onkeyup="filterTable()"></th>
            <th style="display:none;">Pathway Terms<br /><input type="text" id="pwo" style="width:50px;" onkeyup="filterTable()"></th>
            <th style="display:none;">Disease Terms<br /><input type="text" id="dio" style="width:50px;" onkeyup="filterTable()"></th>
            <th style="display:none;">Cell Types<br /><input type="text" id="cto" style="width:50px;" onkeyup="filterTable()"></th>
            <th style="display:none;" >wpid</th>
            <th style="display:none;" >title</th>
            <th style="display:none;" >url</th>
            <th style="display:none;" >firstorg</th>
            <th style="display:none;" >lastedited</th>
            {% assign pw-sorted = site.figures | sort: "title" %}
            {% for pw in pw-sorted %}
              {% assign pw-type-group = pw.annotations | group_by: "type" %}
              <tr>
                <td style="display:none;" title="{{ pw.organisms | join: ", "}}">{{ pw.organisms | join: ", "}}</td>
                <td style="display:none;" title="{{ pw.communities | join: ", "}}">{{ pw.communities | join: ", "}}</td>
                {% for type in type-group %}  
                  {% assign pw-items = "" %}
                  {% for pw-type in pw-type-group %}
                    {% if pw-type.name == type.name %}
                      {% assign pw-items = pw-type.items | map: "value" | join: "|" %}
                      {% assign pw-items-pars = pw-type.items | map: "parent" | join: "|" %}
                      {% if pw-items-pars.size > 0 %}
                        {% capture pw-items %}{{ pw-items | append: "|" | append: pw-items-pars }}{% endcapture %}
                      {% endif %}
                      {% assign pw-items = pw-items | split: "|" | uniq | join: ", " %}
                    {% endif %}
                  {% endfor %}
                  <td style="display:none;" title="{{ pw-items }}">
                    <div style="overflow: hidden; max-height: 50px; white-space: nowrap; text-overflow: ellipsis;">
                    {{ pw-items }}
                    </div>
                  </td>
                {% endfor %}
                <td style="display:none;" >{{ pw.wpid }}</td>
                <td style="display:none;" >{{ pw.title }}</td>
                <td style="display:none;" >{{ pw.url }}</td>
                <td style="display:none;" >{{ pw.organisms.first }}</td>
                <td style="display:none;" >{{ pw.last-edited }}</td> 
              </tr>
            {% endfor %}
        </table>
      </div>
    </div>
  </div> <!-- End hidden table -->
</div>

<script>
// TABLE FILTER
// Declare one-time variables
var sortDiv = document.getElementById("sortable");
var btnMore = $("#cardsMore");
var table = document.getElementById("myTable");
var tr = table.getElementsByTagName("tr");
var cardDiv = document.getElementById('pwcards');
var listDiv = document.getElementById('pwlist');
var fils = {
  'org':0,
  'com':1,
  'pwo':2,
  'dio':3,
  'cto':4
};
// Initialize states
btnMore.hide();
countVisibleRows();
// sortTable(6); // TOO SLOW PRIOR TO FILTERING

function countVisibleRows() {
  var rows = table.rows;
  var z = 0;
  var sort = true;
  for (var q = 1; q < rows.length; q++) {
    if (rows[q].style.display == ""){ //visible row
      z++;
      if (z > 200){ // SORT THRESHOLD
        sort = false;
        break;
      }
    }
  }
  if (sort){
    sortDiv.style.display = ''; 
  } else {
    sortDiv.style.display = 'none'; 
  }
}


function filterTable() {
  // Declare variables
  var activeFils, emptyFils, input, filSplit, td, i, j, txtValue, cardVars;
  activeFils = [];
  emptyFils = [];
  cardVars = {};
  cardVarsMore = [];
  cardDiv.innerHTML = "";
  listDiv.innerHTML = "";
  j = 0;
  btnMore.hide();

  // Define empty and active filter sets
  Object.keys(fils).forEach(key => {
    input = document.getElementById(key).value;
    if (input.length > 0){
      activeFils.push(key);
    } else {
      emptyFils.push(key);
    }
  });
 
  // Loop through all table rows  
  for (var i = 0; i < tr.length; i++) {
    // Loop through column filters
    if(activeFils.length == 0) {
      // Hide all if nothing selected 
      tr[i].style.display = "none";
    } else if (emptyFils.length > 0) {
      // Show all rows if an column filter is empty 
      tr[i].style.display = "";
    }
    // Loop through active column filters
    activeFils.forEach(key => {
      input = document.getElementById(key);
      filSplit = input.value.toUpperCase().split(", ");
      td = tr[i].getElementsByTagName("td")[fils[key]];
      if (td) {
        txtValue = td.textContent || td.innerText;
        tvSplit = txtValue.toUpperCase().trim().split(", ");
        // console.log(tvSplit);
        // console.log(filSplit);
        if (filSplit.filter(value => tvSplit.includes(value)).length > 0  //filter.indexOf(txtValue.toUpperCase()) !== -1  
        && tr[i].style.display != "none") {
          // Show those that match the filter and aren't already hidden
          tr[i].style.display = "";
        } else {
          // Hide those that don't match the filter
          tr[i].style.display = "none";
        }
      } 
    });
    if (tr[i].style.display == "" && i > 0){
      j++;
      cardVars = {};
      cardVars["wpid"] = tr[i].cells[5].innerText;
      cardVars["title"] = tr[i].cells[6].innerText;
      cardVars["url"] = tr[i].cells[7].innerText;
      cardVars["org"] = tr[i].cells[8].innerText;
      addList(cardVars);
      if(j <= 50){
        addCard(cardVars); //display "show more" button and store cards at i > 50
      } else {
        btnMore.show();
        cardVarsMore.push(cardVars);
      }
    }
    // console.log(tr[i]);
  }
  // console.log(cardVarsMore);
  countVisibleRows();
}

// Listen for organism checkboxes
var orgList = []
var interests = document.querySelectorAll("[name=organisms"); 
for (var index = 0; index < interests.length; index++) { 
    interests[index].addEventListener("change", function(evt){ 
        var checkbox = evt.target; 
        //console.log(checkbox.value + " changed to " + checkbox.checked); 
        orgList = $("input:checkbox[name=organisms]:checked").map(function(){return $(this).val()}).get();
        document.getElementById('org').value = orgList;
        filterTable();
    }); 
}
// Listen for community checkboxes
var comList = []
var interests = document.querySelectorAll("[name=communities"); 
for (var index = 0; index < interests.length; index++) { 
    interests[index].addEventListener("change", function(evt){ 
        var checkbox = evt.target; 
        //console.log(checkbox.value + " changed to " + checkbox.checked); 
        comList = $("input:checkbox[name=communities]:checked").map(function(){return $(this).val()}).get();
        document.getElementById('com').value = comList;
        filterTable();
    }); 
}
// Listen for Pathway Ontology checkboxes
var pwoList = []
var interests = document.querySelectorAll("[name=pathway_ontology"); 
for (var index = 0; index < interests.length; index++) { 
    interests[index].addEventListener("change", function(evt){ 
        var checkbox = evt.target; 
        //console.log(checkbox.value + " changed to " + checkbox.checked); 
        pwoList = $("input:checkbox[name=pathway_ontology]:checked").map(function(){return $(this).val()}).get();
        document.getElementById('pwo').value = pwoList;
        filterTable();
    }); 
}
// Listen for Disease checkboxes
var dioList = []
var interests = document.querySelectorAll("[name=disease_ontology"); 
for (var index = 0; index < interests.length; index++) { 
    interests[index].addEventListener("change", function(evt){ 
        var checkbox = evt.target; 
        //console.log(checkbox.value + " changed to " + checkbox.checked); 
        dioList = $("input:checkbox[name=disease_ontology]:checked").map(function(){return $(this).val()}).get();
        document.getElementById('dio').value = dioList;
        filterTable();
    }); 
}
// Listen for Pathway Ontology checkboxes
var ctoList = []
var interests = document.querySelectorAll("[name=cell_type_ontology"); 
for (var index = 0; index < interests.length; index++) { 
    interests[index].addEventListener("change", function(evt){ 
        var checkbox = evt.target; 
        //console.log(checkbox.value + " changed to " + checkbox.checked); 
        ctoList = $("input:checkbox[name=cell_type_ontology]:checked").map(function(){return $(this).val()}).get();
        document.getElementById('cto').value = ctoList;
        filterTable();
    }); 
}


// URL PARAMETERS
var orgList, comList, pwoList, dioList, ctoList;
var url_string = window.location.href;
var url = new URL(url_string);
if (url.searchParams.toString().length > 0){
  orgList = url.searchParams.get("Organism");
  comList = url.searchParams.get("Community");
  pwoList = url.searchParams.get("Pathway Ontology");
  dioList = url.searchParams.get("Disease Ontology");
  ctoList = url.searchParams.get("Cell Type Ontology");
} else {
  // Check org:human by default if no other parameters
  orgList = "Homo sapiens"; 
  comList = null;
  pwoList = null;
  dioList = null;
  ctoList = null;

}  
//console.log(orgList);

// CHECKBOXES
var event = document.createEvent("HTMLEvents");
event.initEvent('change', false, true);

if(null != orgList){
  document.getElementById("organisms").classList.add('show');
  document.getElementById("organisms").classList.remove('hide');  
orgList.split(",").forEach(key => {
  var checkbox = document.querySelectorAll(`input[type='checkbox'][name='organisms'][value=${CSS.escape(key)}]`)[0];
  checkbox.checked = true;
  checkbox.dispatchEvent(event);
});
} else {
  document.getElementById("organisms").classList.add('hide');
  document.getElementById("organisms").classList.remove('show');
}
if(null != comList){
  document.getElementById("communities").classList.add('show');
  document.getElementById("communities").classList.remove('hide'); 
comList.split(",").forEach(key => {
  var checkbox = document.querySelectorAll(`input[type='checkbox'][name='communities'][value=${CSS.escape(key)}]`)[0];
  checkbox.checked = true;
  checkbox.dispatchEvent(event);
});
} else {
  document.getElementById("communities").classList.add('hide');
  document.getElementById("communities").classList.remove('show');
}
if(null != pwoList){
  document.getElementById("pathway_ontology").classList.add('show');
  document.getElementById("pathway_ontology").classList.remove('hide'); 
pwoList.split(",").forEach(key => {
  var checkbox = document.querySelectorAll(`input[type='checkbox'][name='pathway_ontology'][value=${CSS.escape(key)}]`)[0];
  checkbox.checked = true;
  checkbox.dispatchEvent(event);
});
} else {
  document.getElementById("pathway_ontology").classList.add('hide');
  document.getElementById("pathway_ontology").classList.remove('show');
}
if(null != dioList){
  document.getElementById("disease_ontology").classList.add('show');
  document.getElementById("disease_ontology").classList.remove('hide'); 
dioList.split(",").forEach(key => {
  var checkbox = document.querySelectorAll(`input[type='checkbox'][name='disease_ontology'][value=${CSS.escape(key)}]`)[0];
  checkbox.checked = true;
  checkbox.dispatchEvent(event);
});
} else {
  document.getElementById("disease_ontology").classList.add('hide');
  document.getElementById("disease_ontology").classList.remove('show');
}
if(null != ctoList){
  document.getElementById("cell_type_ontology").classList.add('show');
  document.getElementById("cell_type_ontology").classList.remove('hide'); 
ctoList.split(",").forEach(key => {
  var checkbox = document.querySelectorAll(`input[type='checkbox'][name='cell_type_ontology'][value=${CSS.escape(key)}]`)[0];
  checkbox.checked = true;
  checkbox.dispatchEvent(event);
});
} else {
  document.getElementById("cell_type_ontology").classList.add('hide');
  document.getElementById("cell_type_ontology").classList.remove('show');
}

// function to add card
function addCard(c){
  cardDiv.innerHTML += '<div class="col-sm-auto">' +
    '<div class="card" style="width: 10rem;">' +
      '<a class="card-link" href="'+c["url"]+'">' +
        '<img class="card-img-top" src="/assets/img/'+c["wpid"]+'/'+c["wpid"]+'-thumb.png" alt="'+c["title"]+'>' +
        '<div class="card-body">' +
          '<p class="card-text">'+c["title"]+' <em>('+c["org"]+')</em></p>' +
  '</div></a></div></div>';
}
// function to add item to list
function addList(c){
  listDiv.innerHTML += '<li><a href="'+c["url"]+'">'+c["title"]+' <em>('+c["org"]+')</em></a></li>';
}

  btnMore.click(function (e) {
    e.preventDefault();
    btnMore.hide();
    var k=0;
    Object.values(cardVarsMore).forEach(val => {
      if(k <= 100){ // show 100 at a time
        addCard(val); 
        cardVarsMore.shift();
      } else {
        btnMore.show();
      }
      k++;
    });
  });

function sortTable(n) {
  var table, rows, switching, q ,r, x, y, shouldSwitch, dir, switchcount = 0;
  var visibleRows = [];
  table = document.getElementById("myTable");
  switching = true;
  dir = "asc";
  rows = table.rows;
  /* Collect visible rows (except the
  first, which contains table headers) */
  for (q = 1; q < rows.length; q++) {
    if (rows[q].style.display == ""){ //visible row
      visibleRows.push(q);
    }
  }
  while (switching) {
    switching = false;
    /* Loop through all VISIBLE table rows (+1) */
    for (r = 0; r < (visibleRows.length -1); r++){
      shouldSwitch = false;
      /* Get the two elements you want to compare,
      one from current row and one from the next: */
      x = rows[visibleRows[r]].getElementsByTagName("TD")[n];
      y = rows[visibleRows[r + 1]].getElementsByTagName("TD")[n];
      /* Check if the two rows should switch place,
      based on the direction, asc or desc: */
      if (dir == "asc") {
        if (n == 9){ // Date
          if (new Date(x.innerHTML) > new Date(y.innerHTML)) {
            shouldSwitch = true;
            break;
          }
        } else {
          if (x.innerHTML.toLowerCase() > y.innerHTML.toLowerCase()) {
            shouldSwitch = true;
            break;
          }
        }
      } else if (dir == "desc") {
        if (n == 9){ // Date
          if (new Date(x.innerHTML) < new Date(y.innerHTML)) {
            shouldSwitch = true;
            break;
          }
        } else {
          if (x.innerHTML.toLowerCase() < y.innerHTML.toLowerCase()) {
            shouldSwitch = true;
            break;
          }
        }
      }
    } // end for each row
    if (shouldSwitch) {
      rows[visibleRows[r]].parentNode.insertBefore(rows[visibleRows[r + 1]], rows[visibleRows[r]]);
      switching = true;
      switchcount ++;
      // recollect visible rows
      visibleRows = [];
      for (q = 1; q < rows.length; q++) {
        if (rows[q].style.display == ""){ //visible row
          visibleRows.push(q);
        }
      }
    } else {
      if (switchcount == 0 && dir == "asc") {
        dir = "desc";
        switching = true;
      }
    }
  } 
  filterTable()
}
</script>
