# CTC Student Help System - Project Plan

## Project Overview
A modern, premium student help web application with React frontend and Spring Boot backend. The system allows students to manage tasks, track courses, generate practice questions, and book tutors.

## Design System

### Color Palette (Dark Mode Premium + Orange Accents)
```
Background Primary:   #0F0F0F (Deep black)
Background Secondary: #1A1A1A (Charcoal)
Background Card:      #242424 (Elevated surface)

Accent Primary:       #F97316 (Orange-500)
Accent Secondary:     #EA580C (Orange-600)
Accent Glow:          rgba(249, 115, 22, 0.3)

Text Primary:         #FAFAFA (Almost white)
Text Secondary:       #A3A3A3 (Gray-400)
Text Muted:           #525252 (Gray-600)

Success:              #22C55E (Green-500)
Warning:              #EAB308 (Yellow-500)
Error:                #EF4444 (Red-500)
Info:                 #3B82F6 (Blue-500)
```

### Visual Effects
- Glassmorphism: `backdrop-blur-xl bg-white/5 border border-white/10`
- Gradient accents: `bg-gradient-to-r from-orange-500 to-orange-600`
- Glow effects: `shadow-[0_0_40px_rgba(249,115,22,0.3)]`
- Smooth transitions: `transition-all duration-300 ease-out`

### Typography
- Headings: Inter, font-weight 700-800
- Body: Inter, font-weight 400-500
- Monospace: JetBrains Mono (for code/questions)

## Project Structure

```
ctc-app/                       # React + Vite Application
├── src/
│   ├── assets/               # Images, fonts, icons
│   ├── components/           # Reusable components
│   │   ├── Layout.jsx
│   │   ├── TaskItem.jsx
│   │   ├── TopicItem.jsx
│   │   ├── TutorCard.jsx
│   │   ├── ProgressBar.jsx
│   │   ├── NotificationsPanel.jsx
│   │   └── SkeletonLoader.jsx
│   ├── pages/                # Page components
│   │   ├── LoginPage.jsx
│   │   ├── RegisterPage.jsx
│   │   ├── Dashboard.jsx
│   │   ├── TasksPage.jsx
│   │   ├── CoursesPage.jsx
│   │   ├── PracticePage.jsx
│   │   └── TutorsPage.jsx
│   ├── services/             # API services
│   │   └── api.js
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
├── index.html
├── package.json
├── tailwind.config.js
├── postcss.config.js
└── vite.config.js

```

## Backend Structure

```
ctc-backend/                       # Spring Boot Application
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── ctcapp/
│   │   │           ├── CtcAppApplication.java
│   │   │           ├── controllers/       # REST API Controllers
│   │   │           │   ├── UserController.java
│   │   │           │   ├── TaskController.java
│   │   │           │   ├── CourseController.java
│   │   │           │   ├── PracticeController.java
│   │   │           │   ├── BookingController.java
│   │   │           │   └── NotificationController.java
│   │   │           ├── services/          # Business Logic
│   │   │           │   ├── UserService.java
│   │   │           │   ├── TaskService.java
│   │   │           │   ├── CourseService.java
│   │   │           │   ├── PracticeService.java
│   │   │           │   ├── BookingService.java
│   │   │           │   └── NotificationService.java
│   │   │           ├── repositories/      # Data Access Layer
│   │   │           │   ├── UserRepository.java
│   │   │           │   ├── TaskRepository.java
│   │   │           │   ├── CourseRepository.java
│   │   │           │   ├── TopicRepository.java
│   │   │           │   ├── PracticeQuestionRepository.java
│   │   │           │   └── TutorBookingRepository.java
│   │   │           ├── models/            # Domain Models (No JPA)
│   │   │           │   ├── User.java
│   │   │           │   ├── Task.java
│   │   │           │   ├── Course.java
│   │   │           │   ├── Topic.java
│   │   │           │   ├── Question.java
│   │   │           │   ├── Tutor.java
│   │   │           │   └── Booking.java
│   │   │           └── config/            # Configuration
│   │   │               └── DatabaseConfig.java
│   │   └── resources/
│   │       └── application.properties
│   └── test/
├── pom.xml
└── README.md
```

## Database Abstraction Strategy

### Repository Pattern
The backend uses a clean repository pattern with interfaces to ensure database agnosticism:

```java
// Generic repository interface - works with any database
public interface UserRepository {
    User findById(String id);
    User findByEmail(String email);
    List<User> findAll();
    User save(User user);
    void delete(String id);
}

// MarvinDB implementation (placeholder)
@Repository
@Profile("marvin")
public class MarvinUserRepository implements UserRepository {
    // Implementation using MarvinDB
}

// MongoDB implementation (future)
@Repository
@Profile("mongo")
public class MongoUserRepository implements UserRepository {
    // Implementation using MongoTemplate
}
```

### Domain Models (Plain POJOs - Document-Friendly)
Models are simple Java objects without database annotations, designed for easy MongoDB migration:

```java
// User.java
public class User {
    private String id;              // UUID or MongoDB ObjectId
    private String name;
    private String email;
    private String password;        // Hashed
    private String level;           // Student level/grade
    private String semester;
    private boolean tutorAvailability;
    private LocalDateTime createdAt;
}

// Task.java
public class Task {
    private String id;
    private String userId;          // Reference to User
    private String title;
    private String description;
    private LocalDate date;
    private LocalTime time;
    private String status;          // PENDING, COMPLETED, OVERDUE
}

// Course.java
public class Course {
    private String id;
    private String userId;          // Reference to User
    private String courseName;
    private String semester;
    private String level;
}

// Topic.java
public class Topic {
    private String id;
    private String courseId;        // Reference to Course
    private String topicName;
    private boolean completionStatus;
}

// PracticeQuestion.java
public class PracticeQuestion {
    private String id;
    private String courseId;        // Reference to Course
    private String topicId;         // Reference to Topic
    private String questionText;
}

// Tutor.java (referenced by TutorBooking)
public class Tutor {
    private String id;
    private String name;
    private String email;
    private List<String> subjects;
    private boolean available;
}

// TutorBooking.java
public class TutorBooking {
    private String id;
    private String studentId;       // Reference to User
    private String tutorId;         // Reference to Tutor
    private String courseId;        // Reference to Course
    private String sessionType;     // ONLINE, IN_PERSON
    private LocalDate date;
    private LocalTime time;
    private String status;          // PENDING, CONFIRMED, CANCELLED
}
```

### Database Schema (MarvinDB Placeholder)

| Collection | Fields |
|------------|--------|
| **Users** | `id`, `name`, `email`, `password`, `level`, `semester`, `tutorAvailability` |
| **Tasks** | `id`, `userId`, `title`, `description`, `date`, `time`, `status` |
| **Courses** | `id`, `userId`, `courseName`, `semester`, `level` |
| **Topics** | `id`, `courseId`, `topicName`, `completionStatus` |
| **PracticeQuestions** | `id`, `courseId`, `topicId`, `questionText` |
| **TutorBookings** | `id`, `studentId`, `tutorId`, `courseId`, `sessionType`, `date`, `time` |

**MongoDB Migration Ready:**
- All IDs are Strings (compatible with ObjectId)
- No JPA annotations to remove later
- Document references use String foreign keys
- Flat document structure for easy migration

### Backend Dependencies (pom.xml)
```xml
<dependencies>
    <!-- Spring Boot Starter Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    
    <!-- Spring Boot Starter Validation -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
    
    <!-- Spring Boot Starter Security (for password hashing) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    
    <!-- MarvinDB (placeholder dependency) -->
    <dependency>
        <groupId>com.marvin</groupId>
        <artifactId>marvin-db</artifactId>
        <version>1.0.0</version>
    </dependency>
    
    <!-- MongoDB (commented out for future use) -->
    <!--
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    -->
    
    <!-- Lombok (optional, for boilerplate reduction) -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

## Dependencies

### Core
- react ^18.2.0
- react-dom ^18.2.0
- react-router-dom ^6.20.0

### Styling
- tailwindcss ^3.3.0
- postcss
- autoprefixer

### Animation
- framer-motion ^10.16.0

### HTTP & Notifications
- axios ^1.6.0
- react-hot-toast ^2.4.0

### Icons
- lucide-react ^0.294.0

### Utilities
- clsx ^2.0.0
- tailwind-merge ^2.0.0

## Page Specifications

### 1. LoginPage.jsx
- Full-screen dark gradient background
- Centered glassmorphism card
- Animated form fields with focus effects
- Orange accent button with glow
- Link to register page
- Form validation with shake animation on error

### 2. RegisterPage.jsx
- Similar layout to LoginPage
- Additional fields: name, confirm password
- Password strength indicator
- Terms acceptance checkbox
- Success animation on registration

### 3. Dashboard.jsx
- Welcome header with user name
- Stats cards (tasks due, courses active, tutors available)
- Recent tasks list (mini view)
- Upcoming sessions widget
- Progress overview chart
- Quick action buttons

### 4. TasksPage.jsx
- Task creation form (modal/sidebar)
- Filter tabs: All | Active | Completed | Overdue
- Task list with TaskItem components
- Due date sorting
- Priority indicators (High/Medium/Low)
- Bulk actions (complete/delete)

### 5. CoursesPage.jsx
- Course cards grid
- Progress tracking per course
- Topic list expansion
- Add/edit course functionality
- Course completion percentage

### 6. PracticePage.jsx
- Topic selection
- Question type selector (MCQ, Fill-in, True/False)
- Interactive question card
- Answer feedback with animation
- Score tracking
- Generate new questions button

### 7. TutorsPage.jsx
- Tutor grid with TutorCard components
- Filter by subject/availability
- Search functionality
- Booking modal with calendar
- Tutor rating display

## Component Specifications

### TaskItem.jsx
- Checkbox with custom orange accent
- Task title with strikethrough on complete
- Due date badge
- Priority indicator (colored dot)
- Hover actions (edit/delete)
- Slide-in animation

### TopicItem.jsx
- Expandable accordion
- Progress bar
- Topic name and description
- Subtopic list
- Completion checkbox

### TutorCard.jsx
- Avatar with status indicator
- Name and specialization
- Rating stars
- Availability badge
- Book button with hover glow
- Glassmorphism card effect

### ProgressBar.jsx
- Animated fill on mount
- Percentage label
- Color based on progress (red/yellow/green/orange)
- Gradient fill option
- Smooth transition

### NotificationsPanel.jsx
- Slide-in from right
- Real-time notification list
- Mark as read functionality
- Clear all button
- Unread badge counter
- Timestamp display

## Animation Specifications

### Page Transitions
- Fade + slide up: 300ms ease-out
- Stagger children: 50ms delay each

### Micro-interactions
- Button hover: scale(1.02) + glow
- Card hover: translateY(-4px) + shadow
- Input focus: border-color transition + subtle glow
- Checkbox: spring animation on check
- Loading: pulse animation

### Skeleton Screens
- Shimmer effect gradient
- Pulse animation
- Matching card layouts

## API Endpoints (Final)

| Method | Endpoint | Controller | Description |
|--------|----------|------------|-------------|
| **POST** | `/register` | UserController | Create new user account |
| **POST** | `/login` | UserController | Authenticate user |
| **GET** | `/tutors` | UserController | List available tutors |
| **POST** | `/tasks` | TaskController | Create new task |
| **PUT** | `/tasks/:id` | TaskController | Update task |
| **DELETE** | `/tasks/:id` | TaskController | Delete task |
| **PATCH** | `/tasks/:id/complete` | TaskController | Mark task complete |
| **POST** | `/courses` | CourseController | Create course |
| **POST** | `/courses/:id/topics` | CourseController | Add topic to course |
| **PATCH** | `/topics/:id/complete` | CourseController | Mark topic complete |
| **GET** | `/questions?course=X&topic=Y` | PracticeController | Get practice questions |
| **POST** | `/bookings` | BookingController | Create tutor booking |
| **GET** | `/notifications` | NotificationController | Get user notifications |

### Endpoint Mappings by Controller

```
UserController:
  POST   /register
  POST   /login
  GET    /tutors

TaskController:
  POST   /tasks
  PUT    /tasks/{id}
  DELETE /tasks/{id}
  PATCH  /tasks/{id}/complete

CourseController:
  POST   /courses
  POST   /courses/{id}/topics
  PATCH  /topics/{id}/complete

PracticeController:
  GET    /questions?course=X&topic=Y

BookingController:
  POST   /bookings

NotificationController:
  GET    /notifications
```

## Responsive Breakpoints

```
Mobile:   < 640px   (Single column, bottom nav)
Tablet:   640px - 1024px   (2 columns, sidebar collapsible)
Desktop:  > 1024px  (Full layout, fixed sidebar)
```

## Implementation Order

1. Project setup and configuration
2. Global styles and theme
3. Layout and navigation
4. Authentication pages (Login/Register)
5. Dashboard with mock data
6. Tasks functionality
7. Courses functionality
8. Practice questions
9. Tutors booking
10. Notifications system
11. Polish animations and responsiveness
12. Testing and refinement

## Success Criteria

- All 7 pages functional and navigable
- Smooth 60fps animations throughout
- Consistent dark theme with orange accents
- Fully responsive on all screen sizes
- Toast notifications working
- Loading states implemented
- Form validation with user feedback
- Premium feel in all interactions
