To enable the ops department to support your new service, we need to know a few things

 1. If ops needs to escalate issues with this service, who do we contact?
 2. How can ops file tickets for this service?  Which JIRA project?
 3. What platform is the service built on?  (Language? Framework?)
 4. If the service has its own data store, how is it backed up? How does one restore from backup?
 5. What is the disaster recovery plan for this service?  Do we need to mirror the infrastructure in our DR site (reston)?
 6. Where does the service send log data? (paths to log file on disk, url of relevant kibana dashboard)
 7. How do we restart the service, if it has a daemon?
 8. How do we healthcheck the service?  How should nagios alerts be configured?
 9. Where can we find runbooks for this service?
 10. What interfaces does the service have? (What ports does it listen on?)
 11. If the service goes down, what impact does that have for users?  Will users see error messages?  Stale content?
 12. What hosts does the service run on?
