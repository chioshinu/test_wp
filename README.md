```php
<?php 
class SyncConsumer implements ConsumerInterface{
    .....
    public function execute(AMQPMessage $msg)
    {
        $message = unserialize($msg->body);
        if (!$job = $this->job_rep->find($message['job'])){
            return true;
        }
        print $this->job_titles[$job->getType()] ."\n";
        if ($job->getStatus() != 0){
            return true;
        }
        $data = unserialize($job->getData());
        $res = false;
        try{
            switch($job->getType()){
                ...........
            }
            $job->setStatus($res ? JobRecord::STATUS_FINISHED : JobRecord::STATUS_ERROR);
            $this->em->persist($job);
            $this->em->flush();
        }
        catch ( \Exception $e){
            print $e->getMessage() . "\n";
            print $e->getTraceAsString() . "\n";
            $job->setStatus(JobRecord::STATUS_ERROR);
            $this->em->persist($job);
            $this->em->flush();
            print "Job ERROR\n";
        }
        return true;
    }
}
```
