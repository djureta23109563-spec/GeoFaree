# GEOFARE — REVISED PROJECT WORKFLOW AND PROGRAMMER INSTRUCTIONS

## 1. PROJECT OVERVIEW

Develop GeoFare, a mobile-based passenger application and web-based driver verification system for GPS-based tricycle fare computation, route monitoring, OCR-based vehicle plate recognition, and trip reporting.

The system supports three primary user roles:

1. **Passenger**
   - Captures the vehicle plate using OCR.
   - Allows the system to identify the vehicle.
   - Starts passenger location acquisition.
   - Selects a destination.
   - Selects Regular Trip or Special Trip (Exclusive Ride).
   - Reviews route, distance, and expected fare.
   - Confirms trip details.
   - Displays a QR code for the driver.

2. **Driver**
   - Does not need to install a dedicated driver mobile application.
   - Uses a smartphone camera or QR scanner to scan the passenger's QR code.
   - Opens the GeoFare mobile website.
   - Reviews and verifies trip details.
   - Confirms the trip through the website.
   - Views relevant trip status and trip information.

3. **System / Backend**
   - Processes OCR results.
   - Identifies and verifies the vehicle.
   - Acquires and records passenger location.
   - Calculates routes and distances.
   - Applies validated fare rules.
   - Generates secure QR references.
   - Records and manages trip status.
   - Activates and monitors GPS route tracking.
   - Records route deviation events.
   - Provides data to the LGU dashboard.

4. **LGU Personnel**
   - Accesses a secure LGU dashboard.
   - Views authorized trip and monitoring records.
   - Reviews route deviation events.
   - Views Regular and Special Trip statistics.
   - Generates reports.
   - Uses authorized fare-rule and review functions, if implemented.

The system must follow the approved GeoFare workflow and must not assume official fare amounts, special-trip surcharges, or route-deviation thresholds without validation by the Municipality of Agoo, La Union.

---

## 2. CORE TRIP TYPES

### 2.1 REGULAR TRIP

A standard trip using the applicable validated regular-trip fare rules.

Requirements:

- Passenger can select Regular Trip.
- System stores `trip_type = REGULAR`.
- System applies validated regular-trip fare rules.
- System calculates the applicable route distance.
- System displays the expected fare and applicable fare-rule version.
- Driver can see that the trip is a Regular Trip.

### 2.2 SPECIAL TRIP — EXCLUSIVE RIDE

A trip in which the passenger hires the entire tricycle for their own transportation.

Requirements:

- Passenger can select Special Trip.
- System stores `trip_type = SPECIAL`.
- System identifies the trip as an exclusive ride.
- System applies validated special/exclusive-trip fare rules.
- System displays the expected fare and applicable fare-rule version.
- Driver can clearly see that the trip is an exclusive ride.
- The trip follows the required passenger and driver confirmation workflow.

Important:

- Do not invent a special-trip fare amount or surcharge.
- The special-trip formula must be based on fare rules validated with the Municipality of Agoo, La Union.
- If no validated special-trip rule is available, the system must return a clear configuration error rather than silently using an arbitrary fare.

---

# 3. PASSENGER-SIDE WORKFLOW

## STEP 1 — OPEN GEOFARE APPLICATION

### Passenger actions

1. Open the GeoFare mobile application.
2. Display the welcome or login screen.
3. Allow login or registration if authentication is included.
4. Navigate to the passenger home screen.
5. Display an option to begin a new trip.

### Expected result

The passenger can access the trip creation workflow.

---

## STEP 2 — CAPTURE VEHICLE LICENSE PLATE USING OCR

### Objective

The passenger captures the tricycle's license plate using the application camera. OCR extracts the plate number and the system attempts to identify the associated vehicle before GPS location acquisition begins.

### Passenger actions

1. Open the GeoFare camera.
2. Grant camera permission when requested.
3. Capture a clear image of the vehicle's license plate.
4. Submit the image for OCR processing.
5. Review the extracted plate number, if displayed.
6. Confirm the extracted result or recapture the image.
7. Retry when OCR cannot confidently identify the plate.

### System requirements

- Request and validate camera permission.
- Open the camera interface.
- Capture and validate the image.
- Process the image through OCR.
- Extract the plate number.
- Normalize the extracted plate number where appropriate.
- Validate the plate format according to configured rules.
- Search the vehicle database.
- Check whether the vehicle is registered or recognized.
- Associate the vehicle reference with the current trip session.
- Display an appropriate error for unclear images, OCR failure, or unrecognized vehicles.
- Never rely only on client-provided plate text; verify the vehicle through the backend.

### Success condition

The system extracts the plate number and identifies or records the vehicle according to the approved verification process.

### Failure conditions

- Camera permission denied.
- No image captured.
- Image is blurry or obstructed.
- OCR cannot read the plate.
- Plate format is invalid.
- Vehicle cannot be found.
- Backend verification fails.

### Next step

Proceed to Step 3 only after the configured vehicle identification process is completed.

---

## STEP 3 — AUTOMATICALLY ACQUIRE PASSENGER CURRENT LOCATION

### Objective

After the plate has been captured and the vehicle identification process has completed, the system begins acquiring the passenger's current GPS location.

The passenger's current location becomes the proposed trip origin.

### Trigger

This step begins when:

- Plate image has been captured.
- OCR has processed the image.
- Vehicle identification has completed according to the configured process.
- The trip session is ready to continue.

### System requirements

1. Request location permission if it has not been granted.
2. Check whether location permission is granted.
3. Check whether location services/GPS are enabled.
4. Retrieve latitude and longitude.
5. Validate availability and configured accuracy requirements.
6. Save the origin coordinates and timestamp to the trip session.
7. Display the current location on the map when required.
8. Handle permission denial, disabled GPS, timeout, and inaccurate location.
9. Allow the passenger to retry location acquisition.
10. Prevent route computation until a valid origin is available.

### Success condition

A valid passenger origin is acquired and associated with the trip session.

### Next step

Proceed to Step 4 — Select Destination.

---

## STEP 4 — SELECT DESTINATION

### Passenger actions

1. Search for a destination.
2. Select a destination from search results or the map.
3. Confirm the destination pin.
4. Allow the passenger to edit or change the destination before fare confirmation.

### System requirements

- Validate that a destination has been selected.
- Store destination coordinates.
- Display the selected destination.
- Handle invalid, unavailable, or ambiguous destinations.

### Expected result

The trip session contains a valid origin and destination.

---

## STEP 5 — SELECT TRIP TYPE

Display two options:

1. Regular Trip
2. Special Trip — Exclusive Ride

### UI requirements

- Use clear labels.
- Provide a short description for each option.
- Require one trip type selection.
- Store the selected type as `REGULAR` or `SPECIAL`.
- Allow the passenger to change the selection before final confirmation.

### Expected result

The system stores the selected trip type and applies the corresponding workflow and fare-rule configuration.

---

## STEP 6 — CALCULATE ROUTE AND DISTANCE

The system calculates the route from the passenger's origin to the selected destination.

### Requirements

- Validate origin and destination.
- Request a route from the configured mapping or routing service.
- Retrieve route geometry.
- Calculate route distance using a consistent unit.
- Display the route on the map.
- Display the route distance.
- Handle unavailable routes and routing failures.
- Do not display unverified distance as a confirmed calculation.

### Expected result

The passenger can review the proposed route and distance.

---

## STEP 7 — COMPUTE EXPECTED FARE

The system calculates the expected fare using:

- Trip type.
- Origin.
- Destination.
- Route distance, when applicable.
- Validated fare rules.
- Fare-rule version.

### Fare service requirements

1. Validate the trip type.
2. Retrieve applicable fare rules.
3. Validate required fare parameters.
4. Calculate the expected fare.
5. Return a fare breakdown where applicable.
6. Return the fare-rule version.
7. Return an error if no validated rule is available.
8. Use consistent rounding and currency handling.
9. Keep the calculation logic independent of the user interface.

### Expected result

The expected fare and fare-rule version are available for passenger review.

---

## STEP 8 — REVIEW FARE AND TRIP DETAILS

Display:

- Trip type.
- Pickup location.
- Destination.
- Route distance.
- Expected fare.
- Fare breakdown, where applicable.
- Fare-rule version.
- Important trip conditions.

The passenger must be able to:

- Confirm the fare and trip details.
- Return to edit the destination.
- Change the trip type.
- Cancel trip creation.

The system must not generate the final trip QR reference until the required passenger confirmation is completed.

---

## STEP 9 — CREATE TRIP AND DISPLAY QR CODE

When the passenger confirms the trip:

1. Validate that required trip details are complete.
2. Validate that the fare calculation is available.
3. Create a pending trip record.
4. Generate a unique trip ID.
5. Generate a secure QR reference or token.
6. Store the trip type and required trip information on the server.
7. Display the QR code to the passenger.
8. Show the trip status as `PENDING` or `AWAITING_DRIVER_VERIFICATION`.
9. Instruct the passenger to show the QR code to the driver.

### Security requirements

- QR code should reference a secure trip record.
- Do not expose unnecessary personal information in the QR payload.
- Use short-lived or controlled-use tokens where appropriate.
- Validate QR information on the server.
- Prevent replay, reuse, and unauthorized modification.

### Expected result

A pending trip with a unique QR reference is available for web-based driver verification.

---

# 4. DRIVER WEB-BASED WORKFLOW

The driver does not need a dedicated mobile application. The driver uses a smartphone camera or QR scanner to open the GeoFare website.

## STEP 10 — SCAN PASSENGER QR CODE

### Driver actions

1. Use the smartphone camera or QR scanner.
2. Scan the QR code shown by the passenger.
3. Open the GeoFare mobile website from the QR link.
4. Allow the website to load the trip verification page.

### System requirements

- Encode a secure GeoFare verification URL or token in the QR code.
- Open a mobile-responsive verification page.
- Validate the QR token on the backend.
- Check whether the trip exists.
- Check whether the trip is pending and eligible for verification.
- Reject expired, invalid, already-used, canceled, or completed trip references.
- Do not trust QR payload values without server-side validation.

### Expected result

The GeoFare website displays the valid trip verification page.

---

## STEP 11 — DISPLAY TRIP VERIFICATION INFORMATION

The website should display the information necessary for driver verification:

- Trip reference.
- Trip type: Regular or Special.
- Special Trip label: Exclusive Ride, when applicable.
- Pickup location.
- Destination.
- Expected fare.
- Relevant vehicle reference.
- Trip validity or expiration status.
- Required confirmation details.

Avoid displaying unnecessary passenger personal information.

### Expected result

The driver can review the trip information before confirming.

---

## STEP 12 — DRIVER VERIFICATION AND CONFIRMATION

### Requirements

- Require the configured driver or vehicle verification process.
- Display the trip type clearly.
- Show Special Trip as an exclusive ride.
- Allow the driver to review the trip details.
- Provide a Confirm Trip action.
- Provide a Reject/Cancel or Report Issue action where required.
- Validate the confirmation request on the backend.
- Prevent duplicate confirmations.
- Record the confirmation event, timestamp, and available driver/vehicle reference.
- Do not silently change the trip type or fare after passenger confirmation.

### Authentication decision

The project must define whether the website uses:

- Driver login;
- Vehicle or driver credential verification; or
- A controlled no-login process with secure single-use trip tokens.

If the system needs reliable attribution of confirmations to individual drivers, authenticated driver identification is required.

### Expected result

The system records the driver verification and transitions the trip to the next valid status.

---

## STEP 13 — UPDATE TRIP STATUS

Suggested lifecycle:

`PENDING → SCANNED → CONFIRMED → ACTIVE → COMPLETED`

Additional statuses:

- `CANCELLED`
- `EXPIRED`
- `REJECTED`
- `INVALID`
- `FAILED`
- `NEEDS_REVIEW`

### Requirements

- Enforce valid state transitions on the backend.
- Record the actor responsible for each change.
- Record timestamps.
- Prevent unauthorized status changes.
- Synchronize status between passenger, driver website, backend, and LGU dashboard.

---

## STEP 14 — ACTIVATE GPS ROUTE MONITORING

GPS route monitoring begins only after the required trip confirmation and activation conditions are met.

### Requirements

- Capture GPS updates according to the configured interval.
- Record coordinates and timestamps.
- Associate monitoring records with the correct trip.
- Compare actual route data with the expected route when applicable.
- Handle temporary GPS loss.
- Mark missing or stale location data.
- Stop or finalize monitoring according to the trip lifecycle.
- Preserve relevant monitoring data for reporting and review.

### Important distinction

The passenger's initial GPS location is acquired during trip creation. Continuous route monitoring begins after the trip is confirmed and activated, according to the approved workflow.

---

## STEP 15 — COMPLETE TRIP

When the completion condition is met:

1. Update trip status to `COMPLETED`.
2. Record completion timestamp.
3. Save final trip details.
4. Save relevant route monitoring data.
5. Preserve expected fare and fare-rule version.
6. Make the trip available in trip history.
7. Display a trip summary to the passenger.
8. Make the record available to authorized LGU reporting functions.

If driver confirmation of completion is implemented, the backend must validate the action and enforce appropriate authorization.

---

# 5. SYSTEM AND BACKEND WORKFLOW

## STEP 16 — VALIDATE TRIP AND FARE RULES

The backend must validate:

- Supported trip type.
- Required trip parameters.
- Applicable fare-rule configuration.
- Fare-rule version.
- Origin and destination.
- Route distance.
- Trip status.
- User permissions.

The backend must reject unsupported or incomplete fare computations.

---

## STEP 17 — ROUTE AND DISTANCE SERVICE

The backend or approved service layer must:

- Validate origin and destination.
- Request route geometry.
- Calculate or verify route distance.
- Store the distance used for fare calculation.
- Preserve route data needed for monitoring.
- Return clear errors when routing fails.

---

## STEP 18 — SECURE QR SERVICE

The QR service must:

- Generate a unique trip reference.
- Associate the reference with exactly one trip.
- Apply expiration or controlled-use rules.
- Prevent replay and reuse.
- Validate the token server-side.
- Avoid exposing sensitive information.
- Record scan and confirmation events.

---

## STEP 19 — TRIP RECORD SERVICE

Store:

- Trip ID.
- Passenger ID, where applicable.
- Driver ID, when available.
- Vehicle ID.
- OCR plate result and vehicle reference, where required.
- Trip type.
- Origin coordinates.
- Destination coordinates.
- Route distance.
- Expected fare.
- Fare-rule version.
- QR reference.
- Trip status.
- Creation timestamp.
- Scan timestamp.
- Confirmation timestamp.
- Start timestamp.
- Completion timestamp.

---

# 6. LGU DASHBOARD WORKFLOW

The LGU dashboard is a separate secure web interface for authorized municipal personnel.

## STEP 20 — LGU LOGIN

Requirements:

- Secure LGU account authentication.
- Role-based access control.
- Backend authorization.
- Account activation/deactivation.
- Audit logging.
- Restricted access to passenger, driver, vehicle, GPS, and fare records.

---

## STEP 21 — LGU DASHBOARD OVERVIEW

Display authorized summary statistics:

- Total trips.
- Regular trips.
- Special trips.
- Active trips.
- Completed trips.
- Canceled trips.
- Flagged route deviation events.
- Fare review cases, if implemented.
- Registered vehicles, if authorized.

Support date range and trip-type filters.

---

## STEP 22 — TRIP RECORD MONITORING

Allow authorized LGU users to:

- View trip records.
- Filter by date.
- Filter by Regular or Special Trip.
- Filter by status.
- Search by trip or vehicle reference.
- View trip details.
- Review fare-rule versions.
- Review relevant GPS monitoring information.
- View QR scan and confirmation events where authorized.

---

## STEP 23 — ROUTE DEVIATION REVIEW

The system may flag potential route deviations based on configured rules.

Store:

- Deviation event ID.
- Trip ID.
- Vehicle reference.
- Timestamp.
- Expected route reference.
- Actual route segment or relevant data.
- Configured threshold/rule reference.
- Detection result.
- Review status.
- Reviewer reference.
- Review timestamps.
- Notes and supporting evidence.

Suggested statuses:

- Pending Review.
- Under Review.
- Needs Clarification.
- Closed — No Violation Established.
- Referred for Further Action.

A GPS flag must not automatically be treated as a confirmed violation. GPS inaccuracies, signal loss, legitimate route changes, and other explanations must be considered through the approved review process.

---

## STEP 24 — REPORTS AND ANALYTICS

Support authorized reports such as:

- Trip summary report.
- Regular versus Special Trip report.
- Fare computation report.
- Route monitoring report.
- Deviation review report.
- Vehicle trip report.

Reports should include:

- Date range.
- Applied filters.
- Generation timestamp.
- Report reference.
- Authorized user reference.
- Only data the user is permitted to access.

---

# 7. DATABASE REQUIREMENTS

## Suggested core tables

### users

- user_id
- role
- account_status
- created_at
- updated_at

### passengers

- passenger_id
- user_id, if applicable

### drivers

- driver_id
- user_id, if login is implemented
- verification_status

### vehicles

- vehicle_id
- plate_number
- registration/reference status
- created_at
- updated_at

### trips

- trip_id
- passenger_id
- driver_id, nullable until identified
- vehicle_id
- trip_type
- origin_latitude
- origin_longitude
- destination_latitude
- destination_longitude
- route_distance
- expected_fare
- fare_rule_version
- qr_reference
- trip_status
- created_at
- scanned_at
- confirmed_at
- started_at
- completed_at

### gps_records

- gps_record_id
- trip_id
- latitude
- longitude
- accuracy
- recorded_at

### deviation_events

- deviation_event_id
- trip_id
- detection_rule
- detection_timestamp
- event_data
- review_status
- reviewer_id
- reviewed_at
- review_notes

### fare_rules

- fare_rule_id
- trip_type
- rule_version
- effective_date
- rule_configuration
- approval/reference information
- status

### audit_logs

- audit_id
- user_id or system actor
- action
- entity_type
- entity_id
- timestamp
- relevant metadata

---

# 8. SECURITY AND PRIVACY REQUIREMENTS

Implement:

- Server-side validation.
- Role-based authorization.
- Secure QR references.
- QR expiration and replay prevention.
- Protection against unauthorized fare changes.
- Protection against unauthorized trip-status changes.
- Secure handling of GPS data.
- Secure handling of plate and vehicle information.
- Audit logging.
- Data retention rules.
- Appropriate error messages without exposing technical details.

The QR code must not contain unnecessary personal information or trusted fare values that can be modified by the client.

---

# 9. ERROR HANDLING

Handle at minimum:

1. Camera permission denied.
2. OCR failure.
3. Unclear plate image.
4. Unrecognized vehicle.
5. Location permission denied.
6. GPS unavailable.
7. Destination not selected.
8. Route calculation failure.
9. Missing fare rules.
10. Fare calculation failure.
11. QR generation failure.
12. Invalid or expired QR code.
13. Duplicate QR scan or confirmation.
14. Driver verification failure.
15. Network failure.
16. GPS monitoring interruption.
17. Unauthorized status change.
18. Invalid trip lifecycle transition.
19. Report generation failure.
20. Unauthorized LGU access.

Every error must preserve data integrity and provide a user-understandable message.

---

# 10. TESTING REQUIREMENTS

## Functional testing

- Passenger can create a Regular Trip.
- Passenger can create a Special Trip.
- Passenger can capture a plate using OCR.
- System can identify or handle an unrecognized vehicle.
- System acquires passenger GPS after plate processing.
- Passenger can select a destination.
- System calculates route and distance.
- System computes applicable fare.
- Passenger can review and confirm trip details.
- QR code is generated.
- Driver can scan QR using a smartphone.
- QR opens the GeoFare mobile website.
- Driver can view trip details.
- Driver can confirm the trip.
- GPS monitoring activates after confirmation.
- Trip can be completed.
- LGU can view authorized records.

## Security testing

- Invalid QR references are rejected.
- Expired QR references are rejected.
- Reused QR references are rejected.
- Unauthorized status changes are blocked.
- Unauthorized LGU access is blocked.
- Client-side fare changes are rejected.
- Client-side trip-type changes after confirmation are rejected.
- Sensitive data is not exposed through QR payloads.

## Integration testing

- OCR plate result links to the correct vehicle record.
- Vehicle reference links to the trip.
- Passenger and driver views show consistent trip information.
- Trip type remains consistent throughout the lifecycle.
- QR reference maps to the correct trip.
- Driver confirmation updates the backend.
- GPS records link to the correct trip.
- LGU dashboard displays the correct records.
- Reports use the correct date and trip-type filters.

---

# 11. ACCEPTANCE CRITERIA

The system is ready for review when:

1. Passenger can select Regular Trip or Special Trip.
2. Passenger can capture and process the vehicle plate using OCR.
3. System identifies or handles the vehicle according to the configured process.
4. System automatically begins passenger GPS acquisition after plate processing.
5. Passenger can select a destination.
6. System calculates route and distance.
7. System applies validated fare rules.
8. Passenger can review and confirm trip details.
9. System generates a secure QR reference.
10. Driver can scan the QR code without installing a dedicated driver app.
11. QR code opens the GeoFare mobile website.
12. Driver can review trip details.
13. Driver can verify and confirm the trip.
14. Backend validates and records the confirmation.
15. GPS route monitoring begins after required confirmation.
16. Trip status transitions are enforced.
17. Route deviation events are recorded for review.
18. LGU users can access authorized dashboard functions.
19. Reports can be generated using authorized data.
20. Error cases are handled without corrupting records.
21. Fare rules and route-deviation thresholds are validated before production use.
22. Functional, security, and integration tests pass.

---

# 12. IMPLEMENTATION NOTES

- The driver workflow is web-based; a dedicated driver mobile app is not required.
- The QR code should open a secure GeoFare verification page.
- Driver authentication or another controlled verification method must be defined if individual driver attribution is required.
- The passenger's initial GPS acquisition and continuous trip GPS monitoring are separate processes.
- Special Trip means an exclusive ride in which the passenger hires the entire tricycle.
- Do not invent special-trip fare formulas.
- Do not hardcode unverified route-deviation thresholds.
- A detected route deviation is a review flag, not automatically a confirmed violation.
- Fare rules, privacy requirements, retention periods, and operational procedures must be validated with the Municipality of Agoo, La Union.

END OF REVISED GEOFARE WORKFLOW
