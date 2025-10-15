---
layout: page
permalink: /publications/
title: Publications
description: (* means equal contribution)
nav: true
nav_order: 1
---
<!-- _pages/publications.md -->

<div class="sort-controls">
  <div class="btn-group" role="group" aria-label="Sort publications">
    <button type="button" class="btn btn-outline-primary active" id="sort-by-year" onclick="sortPublications('year')">
      <i class="fas fa-calendar-alt"></i> By Year
    </button>
    <button type="button" class="btn btn-outline-primary" id="sort-by-topic" onclick="sortPublications('topic')">
      <i class="fas fa-tags"></i> By Topic
    </button>
  </div>
</div>

<div class="publications" id="publications-container">
{% bibliography -f {{ site.scholar.bibliography }} %}
</div>

<script>
function sortPublications(sortType) {
  const container = document.getElementById('publications-container');
  const yearBtn = document.getElementById('sort-by-year');
  const topicBtn = document.getElementById('sort-by-topic');
  
  // Update button states
  if (sortType === 'year') {
    yearBtn.classList.add('active');
    topicBtn.classList.remove('active');
    sortByYear(container);
  } else if (sortType === 'topic') {
    yearBtn.classList.remove('active');
    topicBtn.classList.add('active');
    sortByTopic(container);
  }
}

function sortByYear(container) {
  // Get all bibliography entries
  const entries = Array.from(container.querySelectorAll('.bibliography li'));
  
  // Sort by year (descending)
  entries.sort((a, b) => {
    const yearA = getYear(a);
    const yearB = getYear(b);
    return yearB - yearA;
  });
  
  // Clear container and rebuild with year sections
  container.innerHTML = '';
  
  let currentYear = null;
  let currentYearDiv = null;
  
  entries.forEach(entry => {
    const year = getYear(entry);
    
    if (year !== currentYear) {
      currentYear = year;
      currentYearDiv = document.createElement('div');
      currentYearDiv.className = 'year-section';
      
      const yearHeader = document.createElement('h2');
      yearHeader.className = 'year-header';
      yearHeader.textContent = year;
      
      const bibliography = document.createElement('div');
      bibliography.className = 'bibliography';
      
      currentYearDiv.appendChild(yearHeader);
      currentYearDiv.appendChild(bibliography);
      container.appendChild(currentYearDiv);
    }
    
    currentYearDiv.querySelector('.bibliography').appendChild(entry);
  });
}

function sortByTopic(container) {
  // Get all bibliography entries
  const entries = Array.from(container.querySelectorAll('.bibliography li'));
  
  // Group by topic (abbr)
  const topicGroups = {};
  
  entries.forEach(entry => {
    const topic = getTopic(entry);
    if (!topicGroups[topic]) {
      topicGroups[topic] = [];
    }
    topicGroups[topic].push(entry);
  });
  
  // Clear container and rebuild with topic sections
  container.innerHTML = '';
  
  // Sort topics alphabetically
  const sortedTopics = Object.keys(topicGroups).sort();
  
  sortedTopics.forEach(topic => {
    const topicDiv = document.createElement('div');
    topicDiv.className = 'topic-section';
    
    const topicHeader = document.createElement('h2');
    topicHeader.className = 'topic-header';
    topicHeader.textContent = getTopicDisplayName(topic);
    
    const bibliography = document.createElement('div');
    bibliography.className = 'bibliography';
    
    // Sort entries within each topic by year (descending)
    topicGroups[topic].sort((a, b) => {
      const yearA = getYear(a);
      const yearB = getYear(b);
      return yearB - yearA;
    });
    
    topicGroups[topic].forEach(entry => {
      bibliography.appendChild(entry);
    });
    
    topicDiv.appendChild(topicHeader);
    topicDiv.appendChild(bibliography);
    container.appendChild(topicDiv);
  });
}

function getYear(entry) {
  // Look for year in the entry text
  const yearMatch = entry.textContent.match(/\b(19|20)\d{2}\b/);
  return yearMatch ? parseInt(yearMatch[0]) : 0;
}

function getTopic(entry) {
  // Look for the abbr badge in the entry
  const abbrElement = entry.querySelector('.abbr');
  if (abbrElement) {
    return abbrElement.textContent.trim();
  }
  
  // Fallback: try to extract from text patterns
  const text = entry.textContent;
  if (text.includes('ICLR')) return 'ICLR';
  if (text.includes('ICML')) return 'ICML';
  if (text.includes('AISTATS')) return 'AISTATS';
  if (text.includes('KDD')) return 'KDD';
  if (text.includes('AAAI')) return 'AAAI';
  if (text.includes('COLING')) return 'COLING';
  if (text.includes('ICDE')) return 'ICDE';
  if (text.includes('ACSSC')) return 'ACSSC';
  if (text.includes('Submitted')) return 'Submitted';
  if (text.includes('TechReport') || text.includes('Technical Report')) return 'TechReport';
  
  return 'Other';
}

function getTopicDisplayName(topic) {
  const displayNames = {
    'ICLR': 'International Conference on Learning Representations (ICLR)',
    'ICML': 'International Conference on Machine Learning (ICML)', 
    'AISTATS': 'International Conference on Artificial Intelligence and Statistics (AISTATS)',
    'KDD': 'ACM SIGKDD Conference on Knowledge Discovery and Data Mining (KDD)',
    'AAAI': 'AAAI Conference on Artificial Intelligence (AAAI)',
    'COLING': 'International Conference on Computational Linguistics (COLING)',
    'ICDE': 'International Conference on Data Engineering (ICDE)',
    'ACSSC': 'Asilomar Conference on Signals, Systems, and Computers (ACSSC)',
    'IEEE-TSP': 'IEEE Transactions on Signal Processing',
    'EJOR': 'European Journal of Operational Research',
    'FnT-OPT': 'Foundations and Trends in Optimization',
    'OJMO': 'Open Journal of Mathematical Optimization',
    'Submitted': 'Submitted Papers',
    'TechReport': 'Technical Reports',
    'Other': 'Other Publications'
  };
  
  return displayNames[topic] || topic;
}

// Initialize the view when page loads
document.addEventListener('DOMContentLoaded', function() {
  // Wait a bit for bibliography to load
  setTimeout(() => {
    sortPublications('year');
  }, 100);
});
</script>

<style>
.sort-controls {
  text-align: center;
  padding: 15px 0;
  margin-bottom: 25px;
  border-bottom: 1px solid #e9ecef;
}

.btn-group {
  display: inline-flex;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  border-radius: 8px;
  overflow: hidden;
}

.btn-outline-primary {
  border: 1px solid #007bff;
  color: #007bff;
  background-color: white;
  padding: 10px 20px;
  text-decoration: none;
  transition: all 0.3s ease;
  cursor: pointer;
  font-weight: 500;
}

.btn-outline-primary:hover {
  background-color: #f8f9fa;
  transform: translateY(-1px);
}

.btn-outline-primary.active {
  background-color: #007bff;
  color: white;
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.1);
}

.btn-outline-primary i {
  margin-right: 8px;
}

.year-section, .topic-section {
  margin-bottom: 40px;
}

.year-header, .topic-header {
  color: #495057;
  border-bottom: 2px solid #007bff;
  padding-bottom: 10px;
  margin-bottom: 20px;
  font-size: 1.5rem;
  font-weight: 600;
}

.topic-header {
  font-size: 1.3rem;
  color: #6c757d;
}

.bibliography {
  padding-left: 0;
}

.bibliography li {
  margin-bottom: 20px;
  padding: 15px;
  border-left: 3px solid #e9ecef;
  transition: border-left-color 0.3s ease;
}

.bibliography li:hover {
  border-left-color: #007bff;
  background-color: #f8f9fa;
}
</style>
