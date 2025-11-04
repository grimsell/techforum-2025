# Weather App Development Standards

## Tech Stack Requirements

- Use React 18.2+ with functional components only
- Use Material-UI (MUI) 5.11+ for all UI components
- Use Emotion for styling via MUI's `sx` prop
- Use React Testing Library + Jest for testing
- Use native fetch API with custom service layers
- Use Create React App build system
- Use npm as package manager

## Component Architecture

### File Organization
- Group components by feature in `src/components/FeatureName/`
- Place reusable components in `src/components/Reusable/`
- Keep API logic in `src/api/` service files
- Place utilities in `src/utilities/` as pure functions
- Store assets in `src/assets/` with organized subdirectories

### Component Structure
- Use functional components with hooks only
- Destructure props with default values
- Use PascalCase for component names
- Use camelCase for variables and functions
- Export components as default exports
- Keep components focused on single responsibility

### Component Template
```javascript
import React from 'react';
import { Box, Typography } from '@mui/material';

const ComponentName = ({ 
  requiredProp, 
  optionalProp = 'defaultValue' 
}) => {
  return (
    <Box>
      <Typography>{requiredProp}</Typography>
    </Box>
  );
};

export default ComponentName;
```

## Code Quality Standards

### React Patterns
- Use hooks for state management (`useState`, `useEffect`, `useReducer`)
- Use `useMemo` for expensive calculations
- Use `useCallback` for function memoization
- Avoid direct state mutations
- Use async/await for asynchronous operations

### Error Handling
- Implement try-catch blocks in API services
- Use error state in components
- Provide user-friendly error messages
- Log errors to console with context
- Validate environment variables at startup

### State Management
- Use separate state variables for different concerns
- Keep state as simple as possible
- Use custom hooks for shared state logic
- Avoid complex nested state objects

### Code Examples
```javascript
// ✅ Good - Separate state concerns
const [todayWeather, setTodayWeather] = useState(null);
const [isLoading, setIsLoading] = useState(false);
const [error, setError] = useState(null);

// ❌ Avoid - Complex nested state
const [appState, setAppState] = useState({
  weather: { today: null, weekly: null },
  ui: { loading: false, error: null }
});
```

## API Integration Standards

### Service Layer Pattern
- Create dedicated service files for external APIs
- Validate environment variables at module level
- Use async/await for API calls
- Implement proper error handling with meaningful messages
- Return consistent data structures

### Environment Variables
- Store API keys in environment variables only
- Validate required environment variables at startup
- Never commit actual API keys to version control
- Provide `.env.example` with placeholder values

### API Service Template
```javascript
const API_KEY = process.env.REACT_APP_API_KEY;

if (!API_KEY) {
  throw new Error('REACT_APP_API_KEY environment variable is required');
}

export async function fetchData(params) {
  try {
    const response = await fetch(`${API_URL}?key=${API_KEY}&${params}`);
    
    if (!response.ok) {
      throw new Error(`API error: ${response.status}`);
    }
    
    return await response.json();
  } catch (error) {
    console.error('API Service Error:', error);
    throw error;
  }
}
```

### Error Handling Pattern
```javascript
// Component error handling
const [error, setError] = useState(null);

useEffect(() => {
  fetchWeatherData(lat, lon)
    .then(setWeatherData)
    .catch(error => {
      setError(error.message);
      console.error('Weather fetch error:', error);
    });
}, [lat, lon]);
```

## Performance Guidelines

### React Optimization
- Use `React.memo` for expensive components that rerender frequently
- Use `useMemo` for expensive calculations
- Use `useCallback` for functions passed as props
- Implement code splitting with `React.lazy` for large components

### Bundle Optimization
- Import only necessary MUI components
- Use tree-shaking friendly imports
- Optimize images and assets
- Monitor bundle size in builds

### Performance Patterns
```javascript
// Memoize expensive calculations
const processedData = useMemo(() => {
  return expensiveDataProcessing(rawData);
}, [rawData]);

// Memoize callback functions
const handleSearch = useCallback((searchTerm) => {
  onSearchChange(searchTerm);
}, [onSearchChange]);

// Lazy load components
const WeeklyForecast = lazy(() => import('./WeeklyForecast/WeeklyForecast'));
```

## Development Workflow

### Code Review Checklist
- All tests pass
- Components follow established patterns
- Error handling is implemented
- Performance implications considered
- Accessibility guidelines followed
- Code is readable and maintainable

### Git Practices
- Use descriptive commit messages
- Create feature branches for new development
- Use pull requests for code review
- Keep commits focused on single changes

### Commit Message Format
```bash
feat: add weather search functionality
fix: resolve API timeout error handling
docs: update component usage examples
test: add unit tests for DataUtils
refactor: simplify weather data processing
```

## Environment Setup

### Required Environment Variables
```bash
REACT_APP_OPENWEATHER_API_KEY=your_api_key_here
REACT_APP_RAPIDAPI_KEY=your_rapidapi_key_here
```

### Development Commands
```bash
# Start development server
npm start

# Run tests in watch mode
npm test

# Create production build
npm run build

# Run tests with coverage
npm test -- --coverage
```

### Project Structure
```
src/
├── components/
│   ├── Search/
│   ├── TodayWeather/
│   ├── WeeklyForecast/
│   └── Reusable/
├── api/
│   └── OpenWeatherService.js
├── utilities/
│   ├── DataUtils.js
│   ├── DatetimeUtils.js
│   └── IconsUtils.js
├── assets/
│   ├── icons/
│   └── images/
├── App.js
└── index.js
```

## Security Guidelines

### API Key Management
- Never hardcode API keys in source code
- Use environment variables for all sensitive data
- Validate environment variables at application startup
- Provide clear error messages for missing configuration

### Data Validation
- Validate all API responses before processing
- Implement input sanitization for user data
- Use TypeScript or PropTypes for type checking
- Handle edge cases and malformed data gracefully

```javascript
// API response validation
export async function fetchWeatherData(lat, lon) {
  if (!lat || !lon || isNaN(lat) || isNaN(lon)) {
    throw new Error('Valid latitude and longitude are required');
  }

  try {
    const response = await fetch(url);
    const data = await response.json();
    
    // Validate response structure
    if (!data.main || !data.weather) {
      throw new Error('Invalid weather data format');
    }
    
    return data;
  } catch (error) {
    console.error('Weather API error:', error);
    throw error;
  }
}