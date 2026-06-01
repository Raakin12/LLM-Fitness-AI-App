# RepsAI

RepsAI is a full-stack AI fitness companion built with Next.js, TypeScript, MongoDB, Clerk, and Google Gemini. The app helps users generate personalized workout plans, save workouts, manage their profile, and track workout consistency through an interactive progress calendar.

The project was built to demonstrate a modern full-stack application using server actions, authenticated user flows, AI-generated content, persistent database models, form validation, file uploads, and responsive UI components.

## Tech Stack

- Next.js 15
- React 18
- TypeScript
- MongoDB
- Mongoose
- Clerk Authentication
- Google Gemini API
- UploadThing
- Tailwind CSS
- shadcn/ui
- React Hook Form
- Zod
- date-fns
- Lucide React

## Core Features

### AI Workout Generation

Users can generate custom workout plans by submitting:

- Workout title
- Age
- Weight
- Height
- Gender
- Fitness goal
- Target muscle group or exercise focus
- Extra workout details

The app sends these inputs to an API route that uses Google Gemini to generate a structured workout plan. The generated response is cleaned, parsed as JSON, and saved as a list of exercises with sets and reps.

### Workout Saving

Generated workouts are stored in MongoDB and linked to the authenticated user. Each workout includes:

- Title
- Description
- Exercise target
- Exercise list
- Sets and reps
- Creation date
- User reference

Users can view their most recent workout and browse saved workouts.

### User Authentication and Onboarding

Authentication is handled with Clerk. After signing in, users complete an onboarding flow where they create their profile.

The onboarding/profile system supports:

- Name
- Username
- Bio
- Profile image
- Account creation and updates
- Profile editing
- Profile deletion through Clerk

### Profile Image Uploads

The app uses UploadThing for profile photo uploads. Users can upload an image, preview it, and save it to their profile.

### Workout Progress Calendar

Users can track workout consistency with an interactive calendar. The calendar allows users to:

- Select completed workout days
- Save workout days by month
- Navigate between months
- View total workout days for the current month
- View total workout days for the year

Progress is persisted in MongoDB through a dedicated progress model.

### Saved Workout Management

Users can view saved workouts and manage workout history. Each saved workout displays the generated exercise plan in a clean card format.

## Application Flow

```text
Sign in / Sign up
        |
        v
Onboarding / Profile Setup
        |
        v
Home Dashboard
        |
        |-- Generate New Workout
        |       |
        |       v
        |   Gemini API generates workout plan
        |       |
        |       v
        |   Workout saved to MongoDB
        |
        |-- View Latest Workout
        |
        |-- View Saved Workouts
        |
        |-- Track Progress Calendar
        |
        |-- View / Edit Profile
```

## Project Structure

```text
LLM-Fitness-AI-App
├── app
│   ├── (auth)
│   │   ├── onboarding
│   │   ├── sign-in
│   │   └── sign-up
│   ├── (root)
│   │   ├── create-workout
│   │   ├── profile
│   │   ├── progress
│   │   ├── saved-workout
│   │   ├── workout
│   │   └── page.tsx
│   ├── api
│   │   ├── generateWorkout
│   │   └── uploadthing
│   ├── globals.css
│   └── favicon.ico
├── components
│   ├── forms
│   ├── shared
│   ├── ui
│   └── workouts
├── constants
├── lib
│   ├── actions
│   ├── models
│   ├── validations
│   ├── mongoose.ts
│   ├── uploadthing.ts
│   └── utils.ts
├── public
│   └── assets
├── middleware.ts
├── next.config.js
├── package.json
├── tailwind.config.js
└── tsconfig.json
```

## Main Pages

### Home

The landing page introduces RepsAI as an all-in-one fitness companion for workout generation, progress tracking, and saved workout management.

### Create Workout

The create workout page contains the AI workout generation form. Users provide body metrics, fitness goals, and target exercises or muscle groups.

### Workout

Displays the most recently generated workout.

### Saved Workouts

Displays the user's saved workout history.

### Progress

Displays the interactive workout calendar and yearly/monthly workout totals.

### Profile

Displays the user's profile information, join date, and account details.

### Edit Profile

Allows users to update their profile image, name, username, and bio.

### Onboarding

Guides new users through profile setup before using the app.

## Data Models

### User

```ts
{
  id: string;
  username: string;
  name: string;
  image?: string;
  bio?: string;
  workouts: ObjectId[];
  onboarded: boolean;
}
```

### Workout

```ts
{
  title: string;
  description?: string;
  exerciseTarget?: string;
  user: ObjectId;
  exercises: {
    name: string;
    sets: number;
    reps: number;
  }[];
  date: Date;
}
```

### Progress

```ts
{
  user: ObjectId;
  year: number;
  month: number;
  days: number[];
  monthlyWorkoutDays: number;
}
```

## API Routes

### Generate Workout

```http
POST /api/generateWorkout
```

Generates a personalized workout plan using Google Gemini.

Example request body:

```json
{
  "title": "Push Day",
  "age": 25,
  "weight": 82,
  "height": 180,
  "gender": "male",
  "goal": "muscle_gain",
  "exerciseTarget": "chest and triceps",
  "description": "Focus on hypertrophy with dumbbells"
}
```

Example response:

```json
{
  "exercises": [
    {
      "name": "Dumbbell Bench Press",
      "sets": 4,
      "reps": 10
    },
    {
      "name": "Incline Dumbbell Press",
      "sets": 3,
      "reps": 12
    }
  ]
}
```

### UploadThing

```http
/api/uploadthing
```

Handles authenticated profile image uploads.

## Server Actions

The app uses Next.js server actions for database operations.

### User Actions

- Fetch user profile
- Create or update user profile
- Mark onboarding as complete
- Revalidate profile paths after update

### Workout Actions

- Create workout
- Fetch all workouts for a user
- Fetch latest workout
- Fetch workout details
- Delete workout
- Count workout days by month or year

### Progress Actions

- Get saved workout days for a month
- Save workout days for a month
- Calculate total workout days for a year

## Form Validation

The app uses Zod with React Hook Form for client-side form validation.

Workout generation validates:

- Required workout title
- Required exercise or muscle target
- Age between 15 and 100
- Weight between 30 kg and 300 kg
- Height between 100 cm and 250 cm
- Gender selection
- Fitness goal selection

Profile validation checks:

- Profile photo URL
- Name length
- Username length
- Bio length

## UI and Styling

The UI is built with Tailwind CSS and reusable component patterns inspired by shadcn/ui.

The app includes:

- Responsive layouts
- Sidebar navigation
- Bottom mobile navigation
- Top navigation
- Reusable form components
- Workout cards
- Calendar UI
- Profile header
- Button, input, textarea, label, form, and tab components

## Environment Variables

Create a `.env.local` file in the root of the project.

```env
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=your_clerk_publishable_key
CLERK_SECRET_KEY=your_clerk_secret_key

MONGODB_URL=your_mongodb_connection_string

GOOGLE_GEMINI_API_KEY=your_google_gemini_api_key

UPLOADTHING_SECRET=your_uploadthing_secret
UPLOADTHING_APP_ID=your_uploadthing_app_id

NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL=/
NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL=/onboarding
```

Do not commit `.env.local` or real API keys to GitHub.

## Running Locally

### 1. Clone the repository

```bash
git clone https://github.com/Raakin12/LLM-Fitness-AI-App.git
cd LLM-Fitness-AI-App
```

### 2. Install dependencies

```bash
npm install
```

### 3. Create environment file

Create a `.env.local` file and add the required Clerk, MongoDB, Gemini, and UploadThing credentials.

### 4. Run the development server

```bash
npm run dev
```

### 5. Open the app

```text
http://localhost:3000
```

## Available Scripts

```bash
npm run dev
```

Runs the app in development mode.

```bash
npm run build
```

Builds the app for production.

```bash
npm run start
```

Starts the production build.

```bash
npm run lint
```

Runs the Next.js lint command.

## What This Project Demonstrates

This project demonstrates practical full-stack engineering skills across:

- AI API integration
- Full-stack TypeScript development
- Next.js App Router architecture
- Server actions
- MongoDB data modeling
- Authenticated user flows
- Form validation
- File upload handling
- Persistent workout storage
- Calendar-based progress tracking
- Reusable UI components
- Responsive application layout

## Future Improvements

- Add loading states while workouts are being generated
- Add structured error messages for failed AI responses
- Add workout editing improvements
- Add exercise categories and filters
- Add weekly workout plan generation
- Add analytics for streaks and consistency
- Add charts for progress trends
- Add unit and integration tests
- Add deployment instructions for Vercel
- Add screenshots and a demo video
- Add OpenAPI-style documentation for API routes

## Project Status

Completed portfolio project demonstrating a production-style AI fitness application with authentication, database persistence, AI-generated workout plans, profile management, image uploads, and progress tracking.
