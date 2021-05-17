# Concurrent-Balance-Deduction

## Object
Manage the balance or inventory in the context of high concurrency transaction, such as sales promotion, to prevent oversell.

## Measure

### 1. Prevent repeated order

Use Redis to implement distributed lock, aiming to ensure idempotence.

#### Key factors of distributed lock

 1. Combine the *SetIfAbsent (setnx)* and *expire* into an an atomic operation, inside the thread id should be the value.
 2. Unlock in the finally block. (The lock can be unlocked in advance)
 3. Prevent the key unlocked by other threads by mistake by comparing the thread id.

Redission is a framework in Java that provide lock function, that covering above factors.

The *redisson-spring-boot-starter* can coexist with *spring-boot-starter-data-redis*.

 **Step 1. Import your dependency as below:**

		<!-- redis -->
		<dependency>
		    <groupId>org.springframework.boot</groupId>
		    <artifactId>spring-boot-starter-data-redis</artifactId>
		</dependency>
		<dependency>
		    <groupId>org.redisson</groupId>
		    <artifactId>redisson-spring-boot-starter</artifactId>
		    <version>3.15.5</version>
		</dependency>

**Step 2. Configure the redis in yml**

	spring:
	  redis:
	    host: 
	    port: 
	    password:

**Step 3. Use the lock and unlock function**

	@Autowired
    private RedissonClient redissonClient;

	public fun() {
		RLock transaction_lock = redissonClient.getLock(transaction_key);
		try {
			transaction_lock.lock();
			...
		} finally {
			transaction_lock.unlock();
		}
	}

### 2. Balance deduction

