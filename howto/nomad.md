
# hashicorp NOMAD -

` A simpler way to orchestrate your containers


- orchestrate any application (disregard container or not / disregard OS )

- increment application conternazation



### start env

nomad agent -dev

( start a web service on 4646 )


# Run a job
* files are in derectory - redis.nomad

nomad job run redis.nomad 



!! - the file looks like:

job "redis" {

	datacenters = ["dc1"]
	
	type = "service"

# job consist of one more groups

# a group consist of one or more tasks
	group "cache" {    
	  network {
		port "db" {
		  to = 6379
		}
	}

	count = 3
	
	update {
	  max_parallel =1
	  health_check = "checks"
	  min_healthy_time = "30s"
	  healthy_deadline = "2m"
	  auto_revert = true
	  auto_promote = true
	  canary = 2
	}
	
	task "redis" {
	  driver = "docker"
	  
	  config {
	    image = "redis:6.2"
		
		ports = ["db"]
	  }
	  
	  resources {
	    cpu = 500
		memory = 256
	  }
	}
  }
}








## life sicle 

update the file
change the amount of containers to 6

# dry run
nomad job plan redis.nomad

# then

nomad job run redis.nomad








### Update of application
- rolling update
- blue/green
- canary


` this will do the following
bring another containers( app run in cannary )
without touching production ones
( update image to version 6.9 -it fails - the image do not exist )
do
nomad job plan redis.nomad - skip
nomad job run redis.nomad











QnA

Can you run a nomad job as a terafrom

For teams that are provisioning their applications with Terrafrom
- There is a provider for terraform
- or using Terraform to create a template file

You could add vars like - what is your env, what is your release sicle , which region
( variables in your terrfaorm file, that creates a template file )	
Then terraform will crate a template file and feed the nomad
Like so, you terrafrom is the front door














