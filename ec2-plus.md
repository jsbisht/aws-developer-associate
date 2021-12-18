# Advanced EC2

## Bootstrapping EC2

Bootstrapping is a process where scripts or other config steps can be run when an instance is first launched. This allows an instance to be brough to service in a particular configured state.

In systems automation, bootstrapping allows the system to self configure or perform some self configuration steps. In AWS this is `EC2 Build Automation`.

This could perform some software installs and post install configs.

### User Data

Bootstrapping is done using user data - accessed via:

<http://169.254.169.254/latest/user-data>

Anything you pass in is executed by the instance OS. `It is excecuted only once on launch`!

- User data is limited to 16 KB in size
- Can be modified when instance is in stopped state
- Its not secure and shouldnt be used for passwords or long term credentials

EC2 doesn't validate the user data. You can tell EC2 to pass in trash data and the data will be passed. The OS needs to understand the user data.

### Boot-Time-To-Service-Time

This is a metric which measures how quickly you are able to bring an instance into service (say accept user requests).

- For AWS Managed AMI's, Launch Time is in minutes
- If you have bootstraping config post launch that takes long time, you could instead have these steps prebaked into the AMI to reduce the bootstraping time post launch. Thus reducing Boot-Time-To-Service-Time.
