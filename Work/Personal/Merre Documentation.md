## What my Potential Client might be interested in:

- What is Merre
- Why I decided to build it
- How this project may indicate that I am skilled?
	- Am I a good full stack developer?
	- Am I a good UI & UX?
- My ability to produce quality products from scratch
 
## What I want to communicate to a potential client:

- The project is about the most difficult thing a full stack developer can take on
- It rivals the best solutions on the market
- I have done some really nice backend development and logic
- The UX is amazing & performance is as well
- I am detail oriented and capable of taking things from start to finish

## Key Priorities:
---
**Functionalities to Highlight**

- Search
- Filter
- Data Structure
- Introduction
- Account
- Post
	- Create Post
	- Manage Post
- Categories
	- Tags related to each category
- Mobile Responsiveness
- Post Page

## Takeaways - Sections:

- What is Merre?
	- [Merre.al](https://marinabay.al) is an online marketplace platform designed for Albania and potentially expanding to the Balkans.  
	- It aims to provide an optimal experience tailored to the Albanian market facilitating buying and selling of various items - with a focus on User Experience - primarily optimized for performance and ease of use.
- Why build a new *Marketplace*?
	1. Performance Issues with Existing Solutions
	2. Lack of Sufficient Filtering in Competing Platforms
	3. Offer a Joyous Experience Maximizing Ease of Use
- Video Image - The Scale of the Project
		- 36 Pages & 200 Components
		- 40 REST API Endpoints built with SvelteKit and ElysiaJS (Bun)
		- Over 150,000 Lines of Code		

*(The Core Functionalities)*

- Fullscreen Image - Homepage
- Video Image 
	- Top Deals Section & Category List
- Personal Involvement in the Project
	- I saw this project as a great learning opportunity, allowing me to experiment with many technologies which I otherwise rarely utilize. I had the chance to thoroughly explore:
		- Database Design
			- Created Complex Many-To-Many relationships and ACID transactions to handle updates of platform posts (products)
			- Utilized Prisma ORM to query the DB in a type-safe manner & for generating complex queries involving multiple entities, often interlinked
		- DevOps & Linux:
			- Dockerized the Platform and implemented Rolling Updates (Zero Downtime) with Coolify with Git Commit web-hooks
			- Database Backups, Service Status Monitoring & Server Redundancies through Docker Swarm Setup (Connected Hetzner VPS to Local Home Server for optimal performance - View **Blog Article**)
		- Email Development (Building Custom Templates) & Email Delivery System Integrations (Resend)
		- Image Upload System & Custom Drag and Drop Sorting System to simplify sorting & upload
		- Internal Messaging Solution allowing Real-Time communication between users w/ Multiple Content Types (Post, Text, Image, etc.)
		- Multiple CRUD Interfaces & Accompanying Functionalities 
		- Authentication System Built from Scratch w/ Cookies
		- Link Actions - Change Password (Forgot Password), Confirm Email & Phone Number 
- Fullscreen Image - User Dashboard (Settings) Grid - Showing different panels
- Video Image - Create Post Interface
- Video Image - Drag and Drop Interface
---
- Messages
	- It was important to develop a powerful messaging solution, allowing users to communicate with each other - without having to rely on external solutions.
	  I created everything from scratch
- Image Grid
	- Small Image - Showing Quick Actions
	- Small Image - Showing mobile view
	- Small Image - Post View and Text Message
	- Small Image - Opening a new chat
---
- Video Image - Docker Files & Deployment Script
- Video Image - Messages
- Image Grid
	- Small Image - Card Post
	- Small Image - List Post x2
- The Post Page - Small Description
- Fullscreen Image - Post Page on Desktop with a small mockup of the phone design on the right
- Feature Grid
	- Image Optimization based on Container Width and Height - Only view full quality when view is set to fullscreen
	- Image Carousel Preview with Full Touch Support for Mobile (Drag & Resize)
	- Fully Responsive UI - Tab Solution for easy navigation and Intersection Observer implementation to make sure price is always visible
- Video Image - Scrolled Down View of the Post Page showing Tag Preview, Category Preview
- Image Grid
	- Small Image - Recently Viewed Posts
	- Small Image - Stacked View: Location Preview Card & Price History Card  
- The Search Interface
	- In a platform such as Merre, it's crucial to allow the user to search for products easily - whether on mobile or desktop - and provide the results **fast**. 
	- Through a careful implementation of caching practices and by following best practices in SSR and incremental data loading with SvelteKit - it feels like the page knows what you want ahead of time.
- Fullscreen Image - Search Page with smaller Mockup on the Right of Mobile Design
- Image Grid
	- Small Image - Filter Display
	- Small Image - Category Highlight
	- Small Image - Select Filter & Year Filter
- Video Image - List View of Posts in Search Page
- Video Image - Currency Switcher
- Categories
	- In order to differentiate between products and allow users to easily filter and find what they're looking for - categories were implemented. When the User creates a Post, they can select it's category, and a list of Tags will be displayed, so the user can insert the necessary information
- Video Image - Categories Page
- User
	- Setup Account
	- Custom User Authentication
	- Settings
	- Recently Viewed & Liked Posts
- 
%% - Custom Emails Designed w/ React Email
	- Email Server Integration - Send Email to Clients w/ Resend
	- Video Image - Display an example
	- Feature Grid
		- Different type of emails that were created %%

### Features
- Complex Filtering and Category-based Attribute System @ Search
- Dynamic Form Rendering & Validation @ Settings
- Docker Compose Setup for Development and Production use-cases
	- With Docker Swarm integration & 
	- Rolling Updates on GitHub Commit through Coolify implementation









## 1. Project Overview

[Pazar.al](http://Pazar.al) is an online marketplace platform designed for Albania and potentially expanding to the Balkans. It aims to provide a Craigslist-like experience tailored for the local market, facilitating buying and selling of various items and services.

## 2. Project Objectives

- Create a user-friendly online marketplace for Albanian users
- Facilitate easy listing and searching of items across multiple categories
- Implement features to enhance user experience and trust in the platform
- Lay the groundwork for future expansion to other Balkan countries

## 3. Target Audience

The platform caters to two main user groups:

- Individual users looking to buy or sell personal items
- Businesses seeking to establish an online presence and reach more customers

## 4. Design and User Experience

The design focuses on simplicity and ease of use, with:

- Intuitive navigation across various product categories
- Simple interface for creating new posts
- Responsive design for seamless use across devices
- Clear communication of the platform's free-to-use nature

## 5. Key Functionalities
Some of the crucial functionalities of the website:

| Internationalization (English + Albanian)  | i18n Library                  |
| ------------------------------------------ | ----------------------------- |
| Integration with Content Management System | Strapi CMS Backend            |
| Dynamic Form Inputs & Custom Validation    | Custom Logic                  |
| Image Compression & Speed Optimizations    | Next Image & Custom Setup     |
| Email Integration for Lead Management      | Custom Email & Resend Library |
| Age Validation & Request Handling          | Custom Logic                  |
| User Authentication and Profiles           | Custom Implementation         |
| Search and Filtering System                | Custom Algorithm              |

## 6. Marketing and Growth Strategy

The project includes a multi-faceted approach to attract and retain users:

- Offering free trials to the first 500 users
- Direct outreach to businesses with tailored value propositions
- Implementing a referral system with invitation-based options
- Focusing on sellers who deal in bulk to create online stores
- Utilizing social media integration for broader reach

## 7. Future Developments

Plans for future enhancements include:

- Expansion to other Balkan countries
- Implementation of a subscription model and Google Ads integration
- Advanced analytics and insights for businesses
- Mobile app development for increased accessibility


---
[write another overview covering the things a potential client of mine would be interested - this is my portfolio not for the potential merre.al customer](<Project Summary
Merre.al is a Craigslist-inspired platform tailored for Albania and potentially expanding to the Balkans. This project showcases my ability to conceptualize, develop, and launch a comprehensive online marketplace.
Technical Expertise
Full-stack development of a responsive web application
Implementation of user authentication and account management
Integration of image support for product listings
Development of local storage solutions for user preferences
Business Strategy
Market analysis and identification of key categories (e.g., vehicles, electronics, real estate)
Development of monetization strategies, including premium subscriptions and partnership programs
Creation of marketing campaigns targeting both businesses and individual users
User Experience Design
Intuitive interface for creating and managing listings
Implementation of search and sort functionalities
Design of business pages with integrated social media promotion tools
Growth and Scalability
Strategy for attracting and retaining the first 1000 users
Plans for expansion from Albania to other Balkan countries
Consideration of future features like in-app purchases and inventory management
Key Achievements
Successful conceptualization and development of a localized online marketplace
Implementation of a freemium model to encourage initial user adoption
Design of a platform catering to both individual users and businesses
Client Benefits
This project demonstrates my ability to:
Develop complex web applications from concept to launch
Create scalable solutions with potential for regional expansion
Implement effective monetization strategies for digital platforms
Design user-centric interfaces that cater to diverse user groups
Analyze market needs and create tailored technological solutions>)

---

### Project Scope

[Merre.al](http://merre.al/) is an online marketplace platform developed for the Albanian market, with potential for expansion to other Balkan countries. The project involved full-stack web application development, focusing on creating a user-friendly interface for buying and selling various products and services.

### Core Functionalities

User authentication and account management system

Listing creation and management for individual users and businesses

Image upload and storage for product listings

Search and filtering capabilities for efficient product discovery

Local storage implementation for user preferences and saved listings

### Technical Stack

The platform was built using modern web technologies, including:

Frontend: Responsive design using HTML5, CSS3, and JavaScript frameworks

Backend: Server-side programming with database integration

Cloud services for scalable hosting and storage solutions

### User Experience Features

Intuitive listing creation process with multi-step form validation

Advanced search functionality with multiple filter options

Responsive design for seamless mobile and desktop experiences

User dashboard for managing listings and account settings

### Business Integration



Integration with social media platforms for broader reach

Analytics dashboard for businesses to track listing performance

### Localization and Scalability

Multi-language support with initial focus on Albanian and English

Modular architecture to facilitate expansion to other Balkan markets

Scalable database design to accommodate growing user base and listings

### Future-Ready Architecture

The platform's architecture was designed with future enhancements in mind, including:

Integration capabilities for in-app purchases

Framework for implementing a subscription-based model

Extensible API for potential third-party integrations


---

===============================================================================
 Language            Files        Lines         Code     Comments       Blanks
===============================================================================
 CSS                     4          919          792           43           84
 Dockerfile              1            9            5            0            4
 HTML                    6           42           20           18            4
 JavaScript              7       114696       114135          313          248
 JSON                   22        18214        18206            0            8
 Makefile                2           24           16            2            6
 Markdown               11          239            0          148           91
 Shell                   3           40           30            1            9
 SVG                    93         1551         1541           10            0
 Plain Text              2          238            0          235            3
 TOML                    1           26           18            1            7
 TSX                     3          281          268            0           13
 TypeScript            266        19734        15894         1475         2365
 XML                     1            7            3            2            2
 YAML                    5         7213         6996            3          214
-------------------------------------------------------------------------------
 Svelte                271        15013        13540          769          704
 |- CSS                 28         4746         4085           90          571
 |- JavaScript         266         7813         6211          351         1251
 |- PostCSS              1            8            7            1            0
 (Total)                          27580        23843         1211         2526
===============================================================================
 Total                 698       178246       171464         3020         3762
===============================================================================