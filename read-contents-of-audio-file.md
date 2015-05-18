Reading contents of audio files
===============================
If you need to process the amplitudes of an audio file, you'll need something like this. Below is an example that handles reading an mp3 file from a URL.


```objective-c
- (NSMutableArray *)readContentsOfFile:(NSURL *)url{
    /*
     Note: currently this code is tested only on mp3 files.
     */
    NSInteger numberOfSteps = 100;
    NSMutableArray *stepValues = [NSMutableArray new];
    
    NSError *error = nil;
    AVAudioFile *audioFile = [[AVAudioFile alloc] initForReading:url error:&error];
    if (error){
        NSLog(@"%@", [error localizedDescription]);
        return nil;
    }
    
    //Figure out packet size (i.e. number of frames in one stride).
    AVAudioFrameCount packetSize = (AVAudioFrameCount) ceil((double) audioFile.length / (double) numberOfSteps);
    
    //Create buffer big enough to hold one packet.
    AVAudioPCMBuffer *buffer = [[AVAudioPCMBuffer alloc] initWithPCMFormat:audioFile.processingFormat frameCapacity:packetSize];
    
    //Take the max sample value in each packet as the packet sample.
    for (NSInteger i = 0; i < numberOfSteps; i++){
        //Read data into buffer
        [audioFile readIntoBuffer:buffer error:nil];
        
        //Find maximum over all frames, assuming there are 2 channels atm.
        float *samplesLeft = buffer.floatChannelData[0];
        float *samplesRight = buffer.floatChannelData[1];
        float maxSoFar = 0;
        for (int j = 0; j < buffer.frameLength; j++){
            //Max value for a frame is the max of all channels
            float fl = fabs(samplesLeft[j]);
            float fr = samplesRight != NULL ? fabs(samplesRight[j]) : 0;
            
            float localMax = MAX(fl, fr);
            maxSoFar = MAX(maxSoFar, localMax);
        }
        
        //Store max value in array as a representative value for the current packet
        [stepValues addObject:[NSNumber numberWithFloat:maxSoFar]];
    }
    return stepValues;
}
```
