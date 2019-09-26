# CAKE TypeScript React Guidelines

Here are some additional* rules that worked well for the CAKE Team while working with Typescript with React.

## Table of Contents
- [CAKE TypeScript React Guidelines](#cake-typescript-react-guidelines)
  - [Table of Contents](#table-of-contents)
  - [Rules](#rules)
  - [React Component Example](#react-component-example)
  - [Helpful links](#helpful-links)
  - [Notes](#notes)

## Rules

- **explicit > implicit**
  - Sometimes the types can be omitted - but ideally types should always be provided if possible.
  - Returning JSX from React component does not have explicit type.
- Use `.tsx` extension for TypeScript files that have JSX, use `.ts` otherwise
- The `className` props should always be present for visual components (so the component's styles can be overridden when using `styled-components`)
- Use `interface`s instead of `type`s for defining objects.

## React Component Example

```tsx
import React, { useState, useEffect } from 'react';

export interface Props {
    children: React.ReactNode;
    className?: string;
    /** optional `onClick` function */
    onClick?: () => void;
    /** number of milliseconds before content is hidden */
    time: number;
}

function HideAfter({
  children,
  className,
  onClick,
  time,
}: Props) {
    const [shouldShow, setShouldShow] = useState<boolean>(false);

    useEffect(() => {
        setShouldShow(true);

        const timerId = setTimeout(() => setShouldShow(false), time);
        return () => {
            clearTimeout(timerId);
        };
    }, [time]);

    if (shouldShow) {
      return (
          <div className={className} onClick={onClick}>
              {children}
          </div>
      );
    }

    return null;
}

HideAfter.defaultProps = {
    time: 1000,
};

export default HideAfter;
```

## Helpful links

- https://github.com/typescript-cheatsheets/react-typescript-cheatsheet
- https://github.com/typescript-cheatsheets/react-typescript-cheatsheet/blob/master/ADVANCED.md

## Notes

*) Typescript files should still follow AirBnB ES6 guidelines.
