# When To Call Code Legacy Code

## Introduction

The following is a discussion into what makes code legacy and how we avoid the trap of legacy code all together.

## What Is Legacy Code?

There are many definitions that exist for what makes code legacy. These definitions can vary wildly.

### Chat GPT's Answer

Legacy code refers to existing software code that was developed using older technologies or programming languages, which may no longer be widely used or supported. Legacy code can be difficult to maintain, update, and integrate with newer technologies, and can also be more prone to bugs and security vulnerabilities due to its age and complexity.

Legacy code can be found in many different types of software systems, ranging from large enterprise applications to small scripts and utilities. Organizations may choose to continue using legacy code because of the high cost and risk associated with rewriting or replacing it, or because it still serves a critical business function.

In some cases, legacy code may be well-designed and well-documented, making it easier to work with and modify. However, in other cases, legacy code may be poorly organized and poorly documented, making it challenging for developers to understand and update.

### Wikipedia's Answer

Legacy code is old computer source code that is no longer supported on the standard hardware and environments, and is a codebase that is in some respect obsolete or supporting something obsolete. Legacy code may be written in programming languages, use frameworks and external libraries, or use architecture and patterns that are no longer considered modern, increasing the mental burden and ramp-up time for software engineers who work on the codebase. Legacy code may have zero or insufficient automated tests, making refactoring dangerous and likely to introduce bugs.[2] Long-lived code is susceptible to software rot, where changes to the runtime environment, or surrounding software or hardware may require maintenance or emulation of some kind to keep working. Legacy code may be present to support legacy hardware, a separate legacy system, or a legacy customer using an old feature or software version.

### Audience

How would you define legacy code?

## A More Precise Definition

I define legacy software as untested software and untested software as hardware that we have no confidence to change. The following is my reasoning...

### What Is Software?

Software is comprised of two sub-words

> Soft - to mean flexable
>
> Ware - to mean a logical system

Therefore we can define software as a flexible logical system.

### What Is Hardware?

Hardware is comprised of two sub-words

> Hard - to mean inflexable
>
> Ware - to mean a logical system

Therefore we can define hardware as an inflexible logical system.

### What Is Untested Code?

Untested code is hardware.

Yes the code may be well designed and recent for someone with domain knowlegde to make changes.

It might be that the code is old but still well designed and there are members of the team that can still work with it.

It might even be well documented.

Do you have the confidence to change it?

How about in a year?

Can you be 100% certain a change won't be breaking?

### The Counter

You can have code confidence with tested code using the testing pyramid.

> Unit testing provides a foundation.
>
> Acceptance testing checks common user paths through the back-end
>
> Integration testing checks the system works as a whole of components
>
> Automated front end testing checks common user paths to ensure the application behaves as a user expects

This gives us confidence to make changes today, tomorrow and years into the future.

If you upgrade the tech stack and the tests pass you have a 99% certainty that it will work when you run it for real.

Alternative implementations and refactoring can be done to the code and provided the tests pass again 99% certainty all will work when you run it for real.

## Conclussion

Well tested code using the testing pryamid makes for software that is no longer legacy as we have the confidence to change it.

## Questions & Discussion
