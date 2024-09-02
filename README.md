# Saksh Appointment Booking System
 


This package provides a comprehensive appointment booking system with features for creating, viewing, updating, and canceling appointments. It also includes functionality for generating various reports and managing notification preferences.
 



# sakshAppointmentBooking
 



The `sakshAppointmentBooking` class provides the following features:



*   **Availability Check:** Check if a time slot is available for booking.

*   **Appointment Creation:** Create a new appointment if the time slot is available and the user has permission.

*   **View Appointments:** View a list of appointments for the logged-in user.

*   **View Appointment by ID:** View the details of a specific appointment by its ID, including calendar links.

*   **Cancel Appointment:** Cancel an appointment if the user has permission.

*   **Update Appointment:** Update an appointment's details if the new time slot is available and the user has permission.

*   **Complete Payment:** Mark an appointment's payment status as completed if the user has permission.

*   **Set Notification Preferences:** Set the notification preferences for the logged-in user.

*   **Get Notification Preferences:** Get the notification preferences for the logged-in user.

*   **Permission Verification:** Verify user permissions before performing actions using a callback function.





# sakshReportService



 The `sakshReportService` class provides the following features:



*   **Daily Report Generation:** Generate a report for all appointments on a specific date.

*   **Weekly Report Generation:** Generate a report for all appointments within a specified date range.

*   **Monthly Report Generation:** Generate a report for all appointments in a specific month and year.

*   **Service Report Generation:** Generate a report for all appointments that include a specific service.

*   **Client Report Generation:** Generate a report for all appointments associated with a specific client ID.

*   **Permission Verification:** Verify user permissions before generating reports using a callback function.

*   **Formatted Reports:** Format the report data with appointment details including client ID, date, time, services, and status.



# sakshReminderService

 



The `sakshReminderService` class provides the following features:



*   **Reminder Calculation:** Calculate reminder times for appointments based on custom intervals and notification preferences.

*   **Reminder Sending:** Send reminders for appointments using a callback function.

*   **Custom Intervals:** Define custom intervals for sending reminders (e.g., 24 hours before, 1 hour before).

*   **Notification Preferences:** Respect user-defined notification preferences (e.g., frequency and types of notifications).











### Classes and Methods

-------------------

 

### sakshAppointmentBooking Class

=============================



The `sakshAppointmentBooking` class is responsible for managing appointment bookings. Below are the details of its methods:



Constructor

-----------



 constructor(loggedInUser, sakshpermissionCallback)



Initializes the class with the logged-in user and a permission callback function.



## Methods

-------



### sakshIsAvailable(date, startTime, endTime)



    async sakshIsAvailable(date, startTime, endTime)



Checks if a time slot is available for booking.



### sakshCreateAppointment(clientID, date, startTime, endTime, services, price, additionalFields = {})



    async sakshCreateAppointment(clientID, date, startTime, endTime, services, price, additionalFields = {})



Creates a new appointment if the time slot is available and the user has permission.



### sakshViewAppointments()



    async sakshViewAppointments()



Returns a list of appointments for the logged-in user.



### sakshViewAppointmentById(id)



    async sakshViewAppointmentById(id)



Returns the details of a specific appointment by its ID, including calendar links.



### sakshCancelAppointment(id)



    async sakshCancelAppointment(id)



Cancels an appointment if the user has permission.



### sakshUpdateAppointment(id, newDate, newStartTime, newEndTime, newService, newPrice)



    async sakshUpdateAppointment(id, newDate, newStartTime, newEndTime, newService, newPrice)



Updates an appointment's details if the new time slot is available and the user has permission.



### sakshCompletePayment(id)



    async sakshCompletePayment(id)



Marks an appointment's payment status as completed if the user has permission.



### sakshSetNotificationPreferences(frequency, types)



    async sakshSetNotificationPreferences(frequency, types)



Sets the notification preferences for the logged-in user.



### sakshGetNotificationPreferences()



    async sakshGetNotificationPreferences()



Gets the notification preferences for the logged-in user.



sakshReminderService Class

==========================



The `sakshReminderService` class is responsible for managing reminders for appointments. Below are the details of its methods:



Constructor

-----------



    constructor(reminderCallback, customIntervals, appointmentBooking)



Initializes the class with a reminder callback function, custom intervals for reminders, and an appointment booking system.



Methods

-------



### sakshGetReminders()



    async sakshGetReminders()



Fetches user appointments and calculates reminder times based on custom intervals and notification preferences. Returns a list of reminders to be sent.



### sakshSendReminder(appointment, intervalLabel)



    async sakshSendReminder(appointment, intervalLabel)



Sends reminders for a given appointment and interval label using the specified notification types.



sakshReportService Class

========================



The `sakshReportService` class is responsible for generating various types of reports based on appointment data. Below are the details of its methods:



Constructor

-----------



    constructor(loggedInUser, role)



Initializes the class with the logged-in user and their role.



Methods

-------



### sakshGenerateDailyReport(date)



    async sakshGenerateDailyReport(date)



Generates a daily report for the specified date.



### sakshGenerateWeeklyReport(startDate, endDate)



    async sakshGenerateWeeklyReport(startDate, endDate)



Generates a weekly report for the specified date range.



### sakshGenerateMonthlyReport(month, year)



    async sakshGenerateMonthlyReport(month, year)



Generates a monthly report for the specified month and year.



### sakshGenerateServiceReport(service)



    async sakshGenerateServiceReport(service)



Generates a report for the specified service.



### sakshGenerateClientReport(clientID)



    async sakshGenerateClientReport(clientID)



Generates a report for the specified client ID.



### sakshFormatReport(appointments, title)



    sakshFormatReport(appointments, title)



Formats the report with the given appointments and title.













Example Usage

-------------



Here's an example of how to use the package:





```

const express = require('express');
const mongoose = require('mongoose');
const SakshAppointment = require('./models/SakshAppointment');
const sakshReportService = require('./sakshReportService');
const sakshReminderService = require('./sakshReminderService');


const app = express();
const port = 3000;

// Connect to MongoDB
mongoose.connect('mongodb://localhost:27017/saksh', { useNewUrlParser: true, useUnifiedTopology: true });

// Middleware to parse JSON
app.use(express.json());
// Dummy permission callback

const permissionCallback = (action, data) => true;

// Dummy reminder callback

const reminderCallback = (appointment, intervalLabel, type) => {
    console.log(`Sending ${type} reminder for appointment with ${appointment.clientID} at ${appointment.date} ${appointment.startTime} - ${intervalLabel}`);
};



// Custom intervals for reminders

const customIntervals = [

    { time: 24 * 60 * 60 * 1000, label: '24 hours' }, // 24 hours before

    { time: 60 * 60 * 1000, label: '1 hour' } // 1 hour before

];

// Create instances of services
const reportService = new sakshReportService('loggedInUser', 'role');
const reminderService = new sakshReminderService(reminderCallback, customIntervals, {
    async sakshViewUserAppointments() {

        return await SakshAppointment.find();

    }

});


// Endpoint to generate daily report

app.get('/report/daily/:date', async (req, res) => {

    try {

        const report = await reportService.sakshGenerateDailyReport(req.params.date);

        res.json(report);

    } catch (error) {

        res.status(500).send(error.message);

    }

});



// Endpoint to generate weekly report

app.get('/report/weekly', async (req, res) => {

    const { startDate, endDate } = req.query;

    try {

        const report = await reportService.sakshGenerateWeeklyReport(startDate, endDate);

        res.json(report);

    } catch (error) {

        res.status(500).send(error.message);

    }

});



// Endpoint to generate monthly report

app.get('/report/monthly', async (req, res) => {

    const { month, year } = req.query;

    try {

        const report = await reportService.sakshGenerateMonthlyReport(parseInt(month), parseInt(year));

        res.json(report);

    } catch (error) {

        res.status(500).send(error.message);

    }

});



// Endpoint to generate service report

app.get('/report/service/:service', async (req, res) => {

    try {

        const report = await reportService.sakshGenerateServiceReport(req.params.service);

        res.json(report);

    } catch (error) {

        res.status(500).send(error.message);

    }

});



// Endpoint to generate client report

app.get('/report/client/:clientID', async (req, res) => {

    try {

        const report = await reportService.sakshGenerateClientReport(req.params.clientID);

        res.json(report);

    } catch (error) {

        res.status(500).send(error.message);

    }

});



// Endpoint to get reminders

app.get('/reminders', async (req, res) => {

    try {

        const reminders = await reminderService.sakshGetReminders();

        res.json(reminders);

    } catch (error) {

        res.status(500).send(error.message);

    }

});



// Start the server

app.listen(port, () => {

    console.log(`Server is running on http://localhost:${port}`);

});







```






Example 2


 


```
// Dummy permission callback

const permissionCallback = (action, data) => {

    // Implement your permission logic here

    // For example, check if the logged-in user has the required role or permissions

    return true; // Allow all actions for demonstration purposes

};



// Create an instance of sakshReportService

const reportService = new sakshReportService('loggedInUser', permissionCallback);



// Endpoint to generate daily report

app.get('/report/daily/:date', async (req, res) => {

    try {

        const report = await reportService.sakshGenerateDailyReport(req.params.date);

        res.json(report);

    } catch (error) {

        res.status(500).send(error.message);

    }

});



// Endpoint to generate weekly report

app.get('/report/weekly', async (req, res) => {

    const { startDate, endDate } = req.query;

    try {

        const report = await reportService.sakshGenerateWeeklyReport(startDate, endDate);

        res.json(report);

    } catch (error) {

        res.status(500).send(error.message);

    }

});



// Endpoint to generate monthly report

app.get('/report/monthly', async (req, res) => {

    const { month, year } = req.query;

    try {

        const report = await reportService.sakshGenerateMonthlyReport(parseInt(month), parseInt(year));

        res.json(report);

    } catch (error) {

        res.status(500).send(error.message);

    }

});



// Endpoint to generate service report

app.get('/report/service/:service', async (req, res) => {

    try {

        const report = await reportService.sakshGenerateServiceReport(req.params.service);

        res.json(report);

    } catch (error) {

        res.status(500).send(error.message);

    }

});



// Endpoint to generate client report

app.get('/report/client/:clientID', async (req, res) => {

    try {

        const report = await reportService.sakshGenerateClientReport(req.params.clientID);

        res.json(report);

    } catch (error) {

        res.status(500).send(error.message);

    }

});



```




# support 

susheel2339 @ gmail.com


# github repo

https://github.com/sakshstore/saksh-appointment-booking
