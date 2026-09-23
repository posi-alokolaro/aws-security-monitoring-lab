# CloudTrail Investigation

## Objective

The goal of this investigation was to verify that AWS CloudTrail could capture both authorized and unauthorized S3 object-level activity generated from the EC2 instance.

The EC2 instance used the IAM role:

`SecurityLabS3LeastPrivilege`

The role was intentionally configured with permissions to read and upload objects to one S3 bucket, but not delete them.

## Test Activity

The following actions were generated from the EC2 instance:

- `PutObject`
- `GetObject`
- `DeleteObject`

Expected results:

| Event | Expected Result |
|---|---|
| PutObject | Allowed |
| GetObject | Allowed |
| DeleteObject | AccessDenied |

## CloudTrail Results

CloudTrail captured all three S3 data events.

### PutObject

- Event Category: `Data`
- Event Source: `s3.amazonaws.com`
- Result: Success
- Error Code: `None`

### GetObject

- Event Category: `Data`
- Event Source: `s3.amazonaws.com`
- Result: Success
- Error Code: `None`

### DeleteObject

- Event Category: `Data`
- Event Source: `s3.amazonaws.com`
- Result: Denied
- Error Code: `AccessDenied`

The denied event confirmed that the EC2 role attempted an action outside its assigned permissions.

The event message stated that no identity-based policy allowed:

`S3:DeleteObject`

## Security Finding

The test successfully validated least-privilege IAM enforcement.

The EC2 instance could perform only the S3 operations that were explicitly permitted by its IAM policy. The unauthorized deletion attempt was blocked and logged by CloudTrail.

## Security Impact

This demonstrates how CloudTrail can be used to:

- Detect unauthorized API activity
- Investigate failed access attempts
- Identify the IAM principal involved
- Validate whether least-privilege policies are functioning correctly
- Provide evidence for security investigations

## Conclusion

The investigation confirmed that AWS IAM and CloudTrail worked together as intended.

Authorized S3 actions were successful, while the unauthorized `DeleteObject` request was denied and recorded for investigation.
