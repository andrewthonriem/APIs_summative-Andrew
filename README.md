# JobLINK

JobLINK is a web application designed to connect employers with job seekers. Employers can post available job vacancies, while job seekers can search, filter, and apply for jobs. The application incorporates the use of the JSearch API from RapidAPI to provide real-time job data and is deployed on a load-balanced infrastructure for high availability.

## Table of Contents
- [Project Overview](#project-overview)
- [Features](#features)
- [External APIs Used](#external-apis-used)
- [Installation and Local Setup](#installation-and-local-setup)
- [Deployment on Web Servers](#deployment-on-web-servers)
- [Load Balancer Configuration](#load-balancer-configuration)
- [Usage](#usage)
- [Error Handling](#error-handling)
- [Challenges and Solutions](#challenges-and-solutions)
- [Credits](#credits)
- [Demo Video](#demo-video)

---

## Project Overview
JobLinK addresses the need for a centralized, user-friendly platform for job posting and searching. Unlike entertainment-based applications, it provides real value by facilitating employment opportunities and simplifying the recruitment process.

The application includes:

- CRUD functionality for job postings
- Search, filter, and sort features for job seekers
- API integration for real-time data management
- Deployment across two web servers with a load balancer for efficient traffic management

---

## Features

### For Employers:
- Create, edit, and delete job postings
- Add detailed job descriptions, requirements, salary, and deadlines
- View applications submitted by job seekers

### For Job Seekers:
- Browse jobs by title, location, or company
- Search, filter, and sort available jobs
- Apply for jobs directly through the application

### Security & Usability:
- API keys and sensitive information are handled securely
- Clean and intuitive user interface
- Error messages and validation feedback for user interactions

---

## External APIs Used

### 1. JSearch API (RapidAPI Marketplace)
- **Purpose:** Fetches real-time job listings, job details, and salary data for companies. Powers the job search functionality in JobLINK.  
- **Official Documentation:** [JSearch API Documentation](https://rapidapi.com)  

**Endpoints Used:**
- `GET /search` – Search jobs by keyword, location, and filters
- `GET /job-details` – Retrieve detailed information for a specific job
- `GET /job-salary` – Get salary information for a job
- `GET /company-job-salary` – Retrieve salary stats for a company

**Example Usage:**
```javascript
const options = {
  method: 'GET',
  headers: {
    'X-RapidAPI-Host': 'jsearch.p.rapidapi.com',
    'X-RapidAPI-Key': process.env.JSEARCH_API_KEY
  }
};

fetch('https://jsearch.p.rapidapi.com/search?query=developer%20jobs%20in%20chicago&page=1&num_pages=1&country=us&date_posted=all', options)
  .then(response => response.json())
  .then(data => {
      console.log(data);
      // Display jobs dynamically
  })
  .catch(err => console.error(err));
