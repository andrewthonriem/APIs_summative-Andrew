# JobLink

JobLink is a web application designed to connect employers with job seekers. Employers can post available job vacancies, while job seekers can search, filter, and apply for jobs. The application incorporates the JSearch API from RapidAPI to provide real-time job data and is deployed on a load-balanced infrastructure for high availability.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Features](#features)
- [External APIs Used](#external-apis-used)
- [Installation and Local Setup](#installation-and-local-setup)
- [Deployment on Web Servers](#deployment-on-web-servers)
- [Load Balancer Configuration](#load-balancer-configuration)
- [Usage](#usage)
- [Error Handling](#error-handling)
- [Technologies Used](#technologies-used)
- [Security Notes](#security-notes)
- [Challenges and Solutions](#challenges-and-solutions)
- [Credits](#credits)
- [Demo Video](#demo-video)

---

## Project Overview

JobLink addresses the need for a centralized, user-friendly platform for job posting and searching. Unlike entertainment-based applications, it provides real value by facilitating employment opportunities and simplifying the recruitment process.

The application includes:

- CRUD functionality for job postings
- Search, filter, and sort features for job seekers
- API integration for real-time data management
- Deployment across two web servers with a load balancer for efficient traffic management
- Secure API key management using separate configuration files

---

## Features

### For Employers:

- Create, edit, and delete job postings
- Add detailed job descriptions, requirements, salary, and deadlines
- View applications submitted by job seekers
- Manage all posted jobs from a centralized dashboard

### For Job Seekers:

- Browse jobs by title, location, or company
- Search, filter, and sort available jobs
- Apply for jobs directly through the application with cover letter
- Track all submitted applications in one place
- View detailed job information including requirements and benefits

### Security & Usability:

- API keys stored in separate configuration files (not in HTML)
- Configuration files excluded from version control via `.gitignore`
- Clean and intuitive user interface with modern design
- Error messages and validation feedback for user interactions
- Responsive design for mobile and desktop

---

## External APIs Used

### JSearch API (RapidAPI Marketplace)

- **Purpose:** Fetches real-time job listings, job details, and salary data. Powers the job search functionality in JobLink.
- **Official Documentation:** [JSearch API Documentation](https://rapidapi.com/letscrape-6bRBa3QguO5/api/jsearch)

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
    'X-RapidAPI-Key': CONFIG.API_KEY,
    'X-RapidAPI-Host': CONFIG.API_HOST
  }
};

fetch(`https://${CONFIG.API_HOST}/search?query=developer&page=1`, options)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

---

## Installation and Local Setup

### Prerequisites:

- Modern web browser (Chrome, Firefox, Safari, Edge)
- Internet connection for API calls
- RapidAPI account with JSearch API subscription

### Steps:

1. **Clone the repository:**

   ```bash
   git clone https://github.com/andrewthonriem/APIs_summative-Andrew.git
   cd joblink
   ```

2. **Set up API configuration:**

   ```bash
   # Copy the example config file
   cp config.example.js config.js
   ```

3. **Add your API key:**

   - Open `config.js` in a text editor
   - Replace `'your_api_key_here'` with your actual RapidAPI key
   - Save the file

4. **Get Your API Key:**

   - Go to [RapidAPI](https://rapidapi.com/)
   - Sign up for a free account
   - Subscribe to the [JSearch API](https://rapidapi.com/letscrape-6bRBa3QguO5/api/jsearch)
   - Copy your API key from the dashboard
   - Paste it into `config.js`

5. **Run the application locally:**

   ```bash
   # Option 1: Double-click index.html in your file explorer

   # Option 2: Use a simple HTTP server
   python -m http.server 8000
   # Then visit http://localhost:8000

   # Option 3: Use Node.js http-server
   npx http-server -p 8000
   ```

6. **Access the application:**
   - Open `http://localhost:8000` in your browser
   - Or simply open `index.html` directly

---

## Deployment on Web Servers

### Deploying to Multiple Servers

1. **Copy project files to both web servers (web-01 and web-02):**

   ```bash
   # Transfer to web-01
   scp -i ~/.ssh/<key> -r ./joblink ubuntu@<web01-private-ip>:/var/www/joblink

   # Transfer to web-02
   scp -i ~/.ssh/<key> -r ./joblink ubuntu@<web02-private-ip>:/var/www/joblink
   ```

2. **Set ownership and permissions:**

   ```bash
   sudo chown -R www-data:www-data /var/www/joblink
   sudo chmod -R 755 /var/www/joblink
   ```

3. **Configure Nginx (on both web servers):**

   ```nginx
   server {
       listen 80;
       server_name andrewonline.tech www.andrewonline.tech;
       root /var/www/joblink;
       index index.html;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

4. **Restart Nginx on both servers:**

   ```bash
   sudo systemctl restart nginx
   ```

---

## Load Balancer Configuration

HAProxy is installed on the load balancer (lb-01) to distribute traffic between web-01 and web-02.

**Load balancing method:** Round-robin

### HAProxy Configuration:

```haproxy
# Backend configuration
backend http_back
    balance roundrobin
    server web01 <web01-private-ip>:80 check
    server web02 <web02-private-ip>:80 check

# Frontend configuration
frontend http_front
    bind *:80
    bind *:443 ssl crt /etc/haproxy/certs/<certificate>.pem
    redirect scheme https code 301 if !{ ssl_fc }
    default_backend http_back
```

**Testing:**

- Access via load balancer public IP
- Requests are automatically distributed between web-01 and web-02
- High availability ensured if one server fails

---

## Usage

### For Employers:

1. Click "Post a Job" button
2. Fill in job details (title, company, location, description, requirements)
3. Submit the job posting
4. View all posted jobs in the dashboard
5. Check applications submitted by job seekers

### For Job Seekers:

1. Browse available jobs on the homepage
2. Use search and filters to find relevant positions
3. Click on a job card to view full details
4. Click "Apply Now" and fill in application form
5. Submit application with cover letter
6. Track all applications in "My Applications" tab

---

## Error Handling

- Handles API failures gracefully with user-friendly messages
- Input validation prevents invalid submissions
- Clear error messages for network issues
- Fallback to local jobs when API is unavailable
- Console logging for debugging during development

---

## Technologies Used

- **Frontend:** HTML5, CSS3 (with CSS Variables), Vanilla JavaScript
- **API Integration:** JSearch API (via RapidAPI)
- **Data Storage:** LocalStorage for persistence
- **Web Server:** Nginx
- **Load Balancer:** HAProxy
- **Deployment:** Ubuntu servers (web-01, web-02, lb-01)

---

## Security Notes

### Critical Security Information:

- **API Key Protection:**
  - API keys are stored in `config.js` (separate from HTML)
  - `config.js` is in `.gitignore` and never committed
  - Use `config.example.js` as a template for setup
  
- **Best Practices:**
  - Never hardcode API keys in HTML or JavaScript files
  - Always use HTTPS in production (configured in HAProxy)
  - Keep `.gitignore` updated
  - Regularly rotate API keys
  
- **File Structure:**
  ```
  joblink/
  ├── index.html           # Main application
  ├── config.js            # API keys (NEVER COMMIT)
  ├── config.example.js    # Config template (safe to commit)
  ├── .gitignore          # Excludes config.js
  └── README.md           # Documentation
  ```

---

## Challenges and Solutions

| Challenge | Solution |
|-----------|----------|
| Nginx conflicts on LB server | Disabled Nginx; HAProxy manages port 80 and 443 |
| Using public IPs in HAProxy backend | Replaced with private IPs of web servers for security |
| Missing editors on Ubuntu | Installed nano and vim for easier configuration |
| API key security concerns | Moved API keys to separate `config.js` file with `.gitignore` |
| API rate limiting | Implemented local job storage as fallback |
| Cross-server deployment | Automated deployment script for consistency |

---

## Credits

- **JSearch API:** [RapidAPI Marketplace](https://rapidapi.com)
- **Web Infrastructure:** ALX Web Infrastructure and API module guides
- **Technologies:** Nginx, HAProxy, JavaScript, HTML5, CSS3
- **Deployment:** Ubuntu 20.04 LTS servers

---

## Demo Video

**Duration:** 2 minutes  
**Content:** Demonstrates searching, filtering, and applying for jobs

**Watch Demo:** [https://youtu.be/kSePS6QEG5Y?si=EVX5NzLt4fTO8NdC](https://youtu.be/kSePS6QEG5Y?si=EVX5NzLt4fTO8NdC)

---

## Links

- **Live Application:** [http://andrewonline.tech/](http://andrewonline.tech/)
- **Repository:** [https://github.com/andrewthonriem/APIs_summative-Andrew.git](https://github.com/andrewthonriem/APIs_summative-Andrew.git)
- **Demo Video:** [https://youtu.be/kSePS6QEG5Y?si=EVX5NzLt4fTO8NdC](https://youtu.be/kSePS6QEG5Y?si=EVX5NzLt4fTO8NdC)

---

## Browser Compatibility

- Chrome/Edge (recommended)  
- Firefox  
- Safari  
- Opera

---

## Support

If you encounter any issues:

1. Ensure `config.js` exists and contains your API key
2. Check browser console for errors (F12)
3. Verify your RapidAPI subscription is active
4. Ensure internet connection for API calls
5. Check that you're using a modern browser
