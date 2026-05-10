---
dewey: 000.4
id: javascript-standards
title: JavaScript Standards
project: global
description: Version: 1.3.0 Last Updated: 2025-09-13 Owner: Frontend Development Team Review Date: 2025-10-13
status: active
tags: [javascript, standards, purpose]
category: 800 — Global Standards
created: 2025-09-13
updated: 2026-04-27
version: 1.0.0
author: CieloVista Software
relativepath: javascript_standards.md
---
# JavaScript Standards

**Version**: 1.3.0  
**Last Updated**: 2025-09-13  
**Owner**: Frontend Development Team  
**Review Date**: 2025-10-13

## Purpose

This document defines JavaScript coding standards, best practices, and style guidelines for all Cielo Vista Software projects.

## Quick Reference

### Essential Rules
- ✅ Use ES6+ features (const/let, arrow functions, destructuring)
- ✅ Implement proper error handling with try-catch
- ✅ Use async/await over Promises
- ✅ Write self-documenting code with descriptive names
- ✅ Add JSDoc for complex functions
- ❌ No var declarations
- ❌ No console.log in production code

### Code Example
```javascript
// ✅ Good
const getUserData = async (userId) => {
    try {
        const { data } = await api.fetchUser(userId);
        return data;
    } catch (error) {
        logger.error('Failed to fetch user data', { userId, error });
        throw new UserFetchError('Unable to retrieve user information');
    }
};

// ❌ Bad
var getUserData = function(userId) {
    return new Promise(function(resolve, reject) {
        api.fetchUser(userId).then(function(response) {
            console.log(response); // No console.log
            resolve(response.data);
        }).catch(function(err) {
            reject(err); // Poor error handling
        });
    });
};
```

## Language Standards

### Variables and Constants

#### Use const by default, let when reassignment needed
```javascript
// ✅ Good
const API_ENDPOINT = '/api/v1';
const users = await fetchUsers();
let currentIndex = 0;

// ❌ Bad  
var API_ENDPOINT = '/api/v1'; // No var
var users = await fetchUsers(); // Should be const
const currentIndex = 0; // Will be reassigned, should be let
```

#### Descriptive naming
```javascript
// ✅ Good
const maxRetryAttempts = 3;
const isTraceEnabled = process.env.NODE_ENV === 'development';
const userPreferences = getUserPreferences();

// ❌ Bad
const max = 3; // Unclear what max represents
const flag = process.env.NODE_ENV === 'development'; // Generic name
const prefs = getUserPreferences(); // Unclear abbreviation
```

### Functions

#### Prefer arrow functions for callbacks and short functions
```javascript
// ✅ Good
const processEntries = (entries) => entries.filter(entry => entry.level === 'error');

const users = await Promise.all(
    userIds.map(id => fetchUser(id))
);

// ✅ Good for methods and constructors
class TraceElement {
    addEntry(entry) {
        return this.processEntry(entry);
    }
}

// ❌ Bad - unnecessary function keyword
const processEntries = function(entries) {
    return entries.filter(function(entry) {
        return entry.level === 'error';
    });
};
```

#### Function length and complexity
```javascript
// ✅ Good - Single responsibility, clear purpose
const validateTraceEntry = (entry) => {
    if (!entry) throw new ValidationError('Entry is required');
    if (!entry.level) throw new ValidationError('Level is required');
    if (!entry.message) throw new ValidationError('Message is required');
    
    return true;
};

const sanitizeEntry = (entry) => {
    return {
        ...entry,
        message: escapeHTML(entry.message),
        data: redactSensitiveData(entry.data)
    };
};

// ❌ Bad - Too many responsibilities
const processEntry = (entry) => {
    // Validation
    if (!entry) throw new ValidationError('Entry is required');
    if (!entry.level) throw new ValidationError('Level is required');
    // ... 10 more validation lines
    
    // Sanitization  
    const sanitized = { ...entry };
    sanitized.message = escapeHTML(entry.message);
    // ... 15 more sanitization lines
    
    // Storage
    this.entries.set(entry.id, sanitized);
    this.updateUI();
    this.notifyListeners();
    // ... more storage logic
    
    return sanitized; // Function is too long and complex
};
```

### Async Programming

#### Use async/await over Promises
```javascript
// ✅ Good
const loadUserData = async (userId) => {
    try {
        const user = await fetchUser(userId);
        const preferences = await fetchUserPreferences(userId);
        const history = await fetchUserHistory(userId);
        
        return { user, preferences, history };
    } catch (error) {
        logger.error('Failed to load user data', { userId, error });
        throw error;
    }
};

// ❌ Bad - Promise chains
const loadUserData = (userId) => {
    return fetchUser(userId)
        .then(user => {
            return fetchUserPreferences(userId)
                .then(preferences => {
                    return fetchUserHistory(userId)
                        .then(history => {
                            return { user, preferences, history };
                        });
                });
        })
        .catch(error => {
            logger.error('Failed to load user data', { userId, error });
            throw error;
        });
};
```

#### Handle async operations properly
```javascript
// ✅ Good - Proper error handling
const processMultipleEntries = async (entries) => {
    const results = [];
    
    for (const entry of entries) {
        try {
            const processed = await processEntry(entry);
            results.push(processed);
        } catch (error) {
            logger.error('Failed to process entry', { entry: entry.id, error });
            // Continue processing other entries
        }
    }
    
    return results;
};

// ✅ Good - Concurrent processing when order doesn't matter
const processEntriesConcurrently = async (entries) => {
    const promises = entries.map(async (entry) => {
        try {
            return await processEntry(entry);
        } catch (error) {
            logger.error('Failed to process entry', { entry: entry.id, error });
            return null; // or some error indicator
        }
    });
    
    const results = await Promise.all(promises);
    return results.filter(result => result !== null);
};
```

### Error Handling

#### Comprehensive error handling strategy
```javascript
// Custom error classes
export class TraceError extends Error {
    constructor(message, code, details = {}) {
        super(message);
        this.name = 'TraceError';
        this.code = code;
        this.details = details;
        this.timestamp = new Date();
    }
}

export class ValidationError extends TraceError {
    constructor(message, field) {
        super(message, 'VALIDATION_ERROR', { field });
        this.name = 'ValidationError';
    }
}

// ✅ Good - Specific error handling
const addTraceEntry = async (entry) => {
    try {
        validateEntry(entry);
        const sanitized = sanitizeEntry(entry);
        await persistEntry(sanitized);
        notifyListeners(sanitized);
        
        return sanitized;
    } catch (error) {
        if (error instanceof ValidationError) {
            // Handle validation errors specifically
            throw new TraceError('Invalid entry data', 'INVALID_ENTRY', {
                originalError: error,
                entry: entry
            });
        } else if (error instanceof NetworkError) {
            // Handle network errors
            throw new TraceError('Failed to save entry', 'STORAGE_ERROR', {
                originalError: error,
                retry: true
            });
        } else {
            // Handle unexpected errors
            logger.error('Unexpected error in addTraceEntry', error);
            throw new TraceError('Internal error occurred', 'INTERNAL_ERROR');
        }
    }
};
```

### Object and Array Operations

#### Use modern JavaScript features
```javascript
// ✅ Good - Destructuring
const { level, direction, message, data } = entry;
const [first, ...rest] = entries;

// ✅ Good - Spread operator
const updatedEntry = { ...entry, timestamp: new Date() };
const allEntries = [...existingEntries, ...newEntries];

// ✅ Good - Array methods
const errorEntries = entries.filter(entry => entry.level === 'error');
const entryIds = entries.map(entry => entry.id);
const hasErrors = entries.some(entry => entry.level === 'error');
const allValid = entries.every(entry => validateEntry(entry));

// ✅ Good - Optional chaining and nullish coalescing
const userName = user?.profile?.name ?? 'Unknown User';
const maxEntries = config?.trace?.maxEntries ?? 1000;
```

### Classes and Modules

#### Modern class syntax
```javascript
// ✅ Good
export class TraceManager {
    #entries = new Map(); // Private field
    #maxEntries = 1000;
    
    constructor(options = {}) {
        this.#maxEntries = options.maxEntries ?? 1000;
        this.listeners = new Set();
    }
    
    async addEntry(entry) {
        this.#validateEntry(entry);
        const processed = this.#processEntry(entry);
        
        this.#entries.set(processed.id, processed);
        this.#enforceMaxEntries();
        this.#notifyListeners(processed);
        
        return processed;
    }
    
    // Private methods
    #validateEntry(entry) {
        if (!entry?.level) {
            throw new ValidationError('Entry level is required');
        }
    }
    
    #processEntry(entry) {
        return {
            ...entry,
            id: crypto.randomUUID(),
            timestamp: entry.timestamp ?? new Date()
        };
    }
}
```

#### Module exports/imports
```javascript
// ✅ Good - Named exports
export { TraceManager };
export { ValidationError };
export const TRACE_LEVELS = ['debug', 'info', 'warning', 'error'];

// ✅ Good - Default export for main class
export default class TraceElement extends HTMLElement {
    // Implementation
}

// ✅ Good - Imports
import TraceElement from './trace-element.js';
import { TraceManager, TRACE_LEVELS } from './trace-manager.js';
import { debounce } from '../utils/debounce.js';
```

## Code Quality

### JSDoc Documentation

#### Document complex functions and APIs
```javascript
/**
 * Adds a trace entry with validation and processing
 * @param {TraceEntry} entry - The entry to add
 * @param {string} entry.level - Log level (debug|info|warning|error)
 * @param {string} entry.direction - Direction (outgoing|incoming|user|ui)
 * @param {string} entry.message - Entry message
 * @param {Object} [entry.data] - Optional data payload
 * @returns {Promise<ProcessedEntry>} The processed entry
 * @throws {ValidationError} When entry validation fails
 * @throws {StorageError} When entry cannot be stored
 * @example
 * const entry = await trace.addEntry({
 *   level: 'info',
 *   direction: 'outgoing', 
 *   message: 'API request sent'
 * });
 */
async addEntry(entry) {
    // Implementation
}
```

### Performance Considerations

#### Optimize for common operations
```javascript
// ✅ Good - Memoization for expensive operations
const memoizedFormatter = memoize((entry) => {
    return {
        formatted: `[${entry.timestamp}] ${entry.level}: ${entry.message}`,
        className: `trace-entry trace-${entry.level}`
    };
});

// ✅ Good - Debounce expensive UI updates
const updateUI = debounce(() => {
    this.renderVisibleEntries();
    this.updateScrollPosition();
}, 16); // 60fps

// ✅ Good - Use appropriate data structures
class TraceStore {
    constructor() {
        this.entries = new Map(); // O(1) lookup by ID
        this.entriesByLevel = new Map(); // Indexed for filtering
        this.timeIndex = []; // Sorted array for time-based queries
    }
}
```

### Testing Considerations

#### Write testable code
```javascript
// ✅ Good - Pure functions are easy to test
export const formatEntry = (entry, options = {}) => {
    const timestamp = options.showTimestamp ? 
        `[${entry.timestamp.toISOString()}] ` : '';
    const direction = getDirectionSymbol(entry.direction);
    
    return `${timestamp}${direction} ${entry.level.toUpperCase()}: ${entry.message}`;
};

// ✅ Good - Dependency injection for testability
export class TraceManager {
    constructor(storage = new LocalStorage(), logger = console) {
        this.storage = storage;
        this.logger = logger;
    }
    
    async saveEntry(entry) {
        try {
            await this.storage.save(entry);
        } catch (error) {
            this.logger.error('Failed to save entry', error);
            throw error;
        }
    }
}
```

## Linting Configuration

### ESLint Rules
```javascript
// .eslintrc.js
export default {
    env: {
        browser: true,
        es2022: true
    },
    extends: [
        'eslint:recommended'
    ],
    rules: {
        // Variables
        'no-var': 'error',
        'prefer-const': 'error',
        'no-unused-vars': 'error',
        
        // Functions
        'prefer-arrow-callback': 'error',
        'arrow-spacing': 'error',
        
        // Async
        'prefer-promise-reject-errors': 'error',
        'no-async-promise-executor': 'error',
        
        // Best practices
        'eqeqeq': 'error',
        'no-eval': 'error',
        'no-implied-eval': 'error',
        'curly': 'error',
        
        // Style
        'indent': ['error', 4],
        'quotes': ['error', 'single'],
        'semi': ['error', 'always']
    }
};
```

## Common Patterns

### Resource Management
```javascript
// ✅ Good - Proper resource cleanup
class TraceFileHandler {
    constructor() {
        this.fileHandle = null;
    }
    
    async open(filename) {
        this.fileHandle = await fs.open(filename, 'w');
    }
    
    async write(data) {
        if (!this.fileHandle) {
            throw new Error('File not open');
        }
        await this.fileHandle.write(data);
    }
    
    async close() {
        if (this.fileHandle) {
            await this.fileHandle.close();
            this.fileHandle = null;
        }
    }
}

// Usage with proper cleanup
const handler = new TraceFileHandler();
try {
    await handler.open('trace.log');
    await handler.write(data);
} finally {
    await handler.close();
}
```

### Event Handling
```javascript
// ✅ Good - Event delegation and cleanup
class TraceElement extends HTMLElement {
    constructor() {
        super();
        this.boundHandleClick = this.handleClick.bind(this);
        this.boundHandleKeydown = this.handleKeydown.bind(this);
    }
    
    connectedCallback() {
        this.addEventListener('click', this.boundHandleClick);
        this.addEventListener('keydown', this.boundHandleKeydown);
    }
    
    disconnectedCallback() {
        this.removeEventListener('click', this.boundHandleClick);
        this.removeEventListener('keydown', this.boundHandleKeydown);
    }
    
    handleClick(event) {
        // Event handling logic
    }
    
    handleKeydown(event) {
        // Keyboard handling logic
    }
}
```

## Anti-patterns to Avoid

### Common Mistakes
```javascript
// ❌ Bad - Modifying function parameters
const processEntry = (entry) => {
    entry.timestamp = new Date(); // Mutates input
    entry.id = generateId();
    return entry;
};

// ✅ Good - Return new object
const processEntry = (entry) => {
    return {
        ...entry,
        timestamp: new Date(),
        id: generateId()
    };
};

// ❌ Bad - Nested callbacks (callback hell)
getData((data) => {
    processData(data, (processed) => {
        saveData(processed, (result) => {
            updateUI(result);
        });
    });
});

// ✅ Good - Async/await
const handleData = async () => {
    const data = await getData();
    const processed = await processData(data);
    const result = await saveData(processed);
    updateUI(result);
};

// ❌ Bad - Poor error handling
try {
    const data = await riskyOperation();
    return data.result;
} catch (error) {
    return null; // Silent failure
}

// ✅ Good - Proper error handling
try {
    const data = await riskyOperation();
    return data.result;
} catch (error) {
    logger.error('Risky operation failed', { error });
    throw new OperationError('Failed to complete operation', error);
}
```

## Related Documents

- [Testing Requirements](../code-quality/testing-requirements.md)
- [Performance Standards](../performance/performance-standards.md)
- [Code Review Process](../project/code-review-process.md)

## Changelog

- **v1.3.0** (2025-09-13): Added private class fields, updated async patterns
- **v1.2.0** (2025-08-01): Enhanced error handling guidelines
- **v1.1.0** (2025-07-15): Added JSDoc standards and testing patterns
- **v1.0.0** (2025-06-01): Initial JavaScript standards