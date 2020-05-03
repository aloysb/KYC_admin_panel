##ANDREI NEXT MEETING
###DB
=> When creating a user that does already exists error 401.
Maybe needs a better error down the track for the user to know what is going on

=> I didn't use all the API endpoints (such as user's detail) as it was out of the scope.

=> The color scheme is a bit vibrant.
I did not want to spend time on this but we change it to match client's colors.

=> The dashboard is can be broken down in more components for reusability/scalability further down the track depending on the what's expected from the project.

=> The git workflow might need some adjustement if we work together.


###LIMITATIONS/BUG TO BE SOLVED
[ ] If the reason for rejection is greater than a single line, the header doesn't display well.
[ ] If the user refresh the page, he will have to log in again.
[ ] You can access the dashboard without login (but cannot see or use the database)
[ ] The data table doesn't show all the fields. I selected the most important ones but I do think users should be able to display the fields he wishes. Maybe a 'see details' button for each user.



#TODO
## APP
[ ] Redirect back to login page if JWT expires or is empty
[ ] Deploy the app on github pages
[ ] Write README

## DASHBOARD
### DASHBOARD.vue view
[ ] Refactor by creating components.
[X] Approve/Reject Button should be in separate columns.
[ ] Sucess message on creating a new user (snack-bar)
[x] Change status from 0/1 to icon (
'checked' and 'X')
[x] Format last updated

### Create user
[ ] Add validation
[x] Make it in a modal pop up
[x] Make icon for approve/reject larger 