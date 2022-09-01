# Submit multiple account requests<a name="aft-multiple-account-requests"></a>

Although AWS Control Tower account factory can process a single request at any time, the AFT pipeline allows you to submit multiple account requests\. AFT queues all the requests to be processed by AWS Control Tower account factory, and they are processed in first\-in, first\-out \(FIFO\) order\.

You can create a single account request Terraform file for each account, or you can cascade multiple requests in a single Terraform file\.