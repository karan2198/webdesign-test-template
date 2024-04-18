---
title: "test page"
layout: gridlay
sitemap: false
permalink: /test/
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Test Page</title>
    <!-- Add any necessary CSS here -->
    <style>
        .selected-option {
            margin-right: 12px; /* Add margin between selected options */
        }
        #selected-options {
           padding-top: 10px;
        }
        .cancel-button {
          margin-left: 3px;
        }
    </style>
</head>
<body>

<div id="filter-options">
  <label>Filter by Position:</label>
  <div class="dropdown-center">
    <button class="btn btn-secondary dropdown-toggle" type="button" data-bs-toggle="dropdown"   aria-expanded="false"> Select position</button>
    <ul class="dropdown-menu">
    <li><a class="dropdown-item" href="#"  data-value="Assistant Professor">Assistant Professor</a></li>
    <li><a class="dropdown-item" href="#" data-value="Undergraduate student">Undergraduate student</a></li>
    <li><a class="dropdown-item" href="#"  data-value="MS(R) student">MS(R) Student</a></li>
    <li><a class="dropdown-item" href="#" data-value="PhD student">PhD Student</a></li>
    <li><a class="dropdown-item" href="#" data-value="Research Assistant">Research Assistant</a></li>
    <li><a class="dropdown-item" href="#" data-value="Intern">Intern</a></li>
    </ul>
  </div>
</div>

<!-- Display selected options -->
<div id="selected-options"></div>


<h1> Group Members </h1>

<div id="member-list">
{% assign ap_members = '' | split: '' %}
{% assign us_members = '' | split: '' %}
{% assign msr_members = '' | split: '' %}
{% assign phd_members = '' | split: '' %}
{% assign ra_members = '' | split: '' %}
{% assign int_members = '' | split: '' %}
{% assign oth_members = '' | split: '' %}

{% assign sorted_members = site.data.team | sort: "year" %}

{% for member in sorted_members %}
{% if member.position == 'Assistant Professor' %}
{% assign ap_members = ap_members | push: member %}
{% elsif member.position == 'Undergraduate student' %}
{% assign us_members = us_members | push: member %}
{% elsif member.position == 'MS(R) student' %}
{% assign msr_members = msr_members | push: member %}
{% elsif member.position == 'PhD student' %}
{% assign phd_members = phd_members | push: member %}
{% elsif member.position == 'Research Assistant' %}
{% assign ra_members = ra_members | push: member %}
{% elsif member.position == 'Intern' %}
{% assign int_members = int_members | push: member %}
{% else %}
{% assign oth_members = oth_members | push: member %}
{% endif %}
{% endfor %}

{% assign sorted_members = '' | split: '' | concat: ap_members | concat: phd_members | concat: msr_members | concat: ra_members | concat: us_members | concat: int_members | concat: oth_members %}

{% assign number_printed = 0 %}
{% for member in sorted_members %}
{% if member.display == 1 and member.alumni == 0 %}

{% assign even_odd = number_printed | modulo: 2 %}

{% if even_odd == 0 %}

<div class="row">
{% endif %}

<div class="col-sm-6 clearfix member" data-position="{{ member.position }}">
  <img src="{{ member.image }}" class="img-responsive" width="35%" style="float: left" />
  <h4>{{ member.name }}</h4>
  <i>{{ member.position }}, {{ member.affiliation }} <br>email: {{ member.email }}</i>
  <ul style="overflow: hidden">

{% if member.bio1 != "" %}

<li> {{ member.bio1 }} </li>
{% endif %}
{% if member.bio2 != "" %}
<li> {{ member.bio2 }} </li>
{% endif %}
{% if member.bio3 != "" %}
<li> {{ member.bio3 }} </li>
{% endif %}
{% if member.bio4 != "" %}
<li> {{ member.bio4 }} </li>
{% endif %}

  </ul>
</div>

{% assign number_printed = number_printed | plus: 1 %}

{% if even_odd == 1 %}

</div>
{% endif %}
{% endif %}
{% endfor %}

{% assign even_odd = number_printed | modulo: 2 %}
{% if even_odd == 1 %}

</div>
{% endif %}
</div>

<h2> Alumni </h2>

{% for member in sorted_members %}
{% if member.display == 1 and member.alumni == 1 %}

<div class="col-sm-12 clearfix member" data-position="{{ member.position }}">
  <img src="{{ member.image }}" class="img-thumbnail" width="100px" style="float: left" />
  <h4>{{ member.name }}</h4>
  <i>{{ member.position }}, {{ member.affiliation }} ({{ member.year }}) <br>email: {{ member.email }}</i>
  <h5>{{ member.alumni_current }}</h5>
</div>

{% endif %}
{% endfor %}

<script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.11.6/dist/umd/popper.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/js/bootstrap.bundle.min.js"></script>

<!-- Add any necessary JavaScript here -->
<script>
    document.addEventListener('DOMContentLoaded', function () {
        const dropdownItems = document.querySelectorAll('.dropdown-item');
        const members = document.querySelectorAll('.member');
        const selectedOptionsContainer = document.getElementById('selected-options');
        const selectedPositions = [];

        dropdownItems.forEach(item => {
            item.addEventListener('click', function (event) {
                const selectedValue = event.target.getAttribute('data-value');
                if (selectedValue === 'all') {
                    selectedPositions.splice(0, selectedPositions.length);
                    selectedPositions.push('all');
                } else {
                    const index = selectedPositions.indexOf('all');
                    if (index !== -1) {
                        selectedPositions.splice(index, 1);
                    }
                    if (!selectedPositions.includes(selectedValue)) {
                        selectedPositions.push(selectedValue);
                    }
                }
                filterMembers();
                updateSelectedOptions();
            });
        });

        function filterMembers() {
            members.forEach(member => {
                const memberPositions = member.dataset.position.split(',');
                const displayMember = selectedPositions.includes('all') || selectedPositions.some(position => memberPositions.includes(position));
                member.style.display = displayMember ? 'block' : 'none';
            });
            // If no positions selected, display all members
            if (selectedPositions.length === 0) {
                members.forEach(member => {
                    member.style.display = 'block';
                });
            }
        }

        function updateSelectedOptions() {
            selectedOptionsContainer.innerHTML = ''; // Clear previous selection
            selectedPositions.forEach(position => {
                const optionElement = document.createElement('span');
                optionElement.textContent = position === 'all' ? 'All Positions' : position;
                optionElement.classList.add('selected-option');

                // Add cancel button for selected option
                const cancelButton = document.createElement('button');
                cancelButton.textContent = 'x';
                cancelButton.classList.add('cancel-button');
                cancelButton.addEventListener('click', function () {
                    const index = selectedPositions.indexOf(position);
                    if (index !== -1) {
                        selectedPositions.splice(index, 1);
                    }
                    filterMembers();
                    updateSelectedOptions();
                });

                optionElement.appendChild(cancelButton);
                selectedOptionsContainer.appendChild(optionElement);
            });
        }
    });
</script>

</body>
</html>
