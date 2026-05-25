# Natas OverTheWire Level 1

## Objective

Retrieve the password for the `natas2` user.

## Vulnerability Analysis

- The application attempted to restrict user interaction by disabling right-click functionality using client-side JavaScript.

- Despite this restriction, sensitive information remained exposed within the page source code.

## Exploitation Process

- In the page a message indicated that right-clicking had been blocked.

- The page source was inspected directly using: ctrl+u again

## Observations and Findings

- Disabling right click is not an effective security mechanism

- Client-side JS restrictions can be bypassed easily

## Key Concepts Learned

- Client-side security limitations

-JavaScript event restrictions
