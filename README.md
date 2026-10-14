# Booking System

A console application for managing meeting room bookings.

## Requirements

- .NET 10 SDK

## Run the application

Open a terminal in the folder containing `BookingSystemExam.slnx` and run: 

```powershell
dotnet run --project src/BookingSystem
```

Follow the console menu instructions to manage bookings. Select `0` to exit the application.

## Run the tests

From the same folder, run:

```powershell
dotnet test
```

## Code structure

- `BookingConsole` handles user input and console output.
- `BookingService` handles booking rules, validation and cancellation.
- `FileBookingRepository` handles loading and saving bookings in JSON.

## Debugging report

See [DEBUGGING.md](DEBUGGING.md) for a description of the booking overlap issue and its fix.
