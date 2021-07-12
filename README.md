# #Define S3 ID
locals {
 s3_origin_id = "s3-origin"
}

#Create a bucket to upload your static data like images
resource "aws_s3_bucket" "demonewbucket12345" {
  bucket = "demonewbucket12345"
  acl    = "public-read-write"
  region = "ap-southeast-1"
  
  versioning {
    enabled = true
  }

  tags = {
    Name = "demonewbucket12345"
    Environment = "Prod"
  }

 provisioner "local-exec" {
    command = "git clone https://github.com/vineets300/Webpage1.git web-server-image"
 }

}

#Allow public access to the bucket
resource "aws_s3_bucket_public_access_block" "public_storage" {
 depends_on = [aws_s3_bucket.demonewbucket12345]
 bucket = "demonewbucket12345"
 block_public_acls = false
 block_public_policy = false
}

#Upload your data to S3 bucket
resource "aws_s3_bucket_object" "Object1" {
  depends_on = [aws_s3_bucket.demonewbucket12345]
  bucket = "demonewbucket12345"
  acl    = "public-read-write"
  key = "Demo1.PNG"
  source = "web-server-image/Demo1.PNG"
}

#Create a Cloudfront distribution for CDN
resource "aws_cloudfront_distribution" "tera-cloufront1" {
    depends_on = [ aws_s3_bucket_object.Object1]
    origin {
        domain_name = aws_s3_bucket.demonewbucket12345.bucket_regional_domain_name
        origin_id = local.s3_origin_id
    }   
    enabled = true
      default_cache_behavior {
        allowed_methods = ["DELETE", "GET", "HEAD", "OPTIONS", "PATCH", "POST", "PUT"]
        cached_methods = ["GET", "HEAD"]
        target_origin_id = local.s3_origin_id

        forwarded_values {
            query_string = false
        
            cookies {
               forward = "none"
            }
        }
        viewer_protocol_policy = "allow-all"
        min_ttl = 0
        default_ttl = 3600
        max_ttl = 86400
    }

    restrictions {
        geo_restriction {
           restriction_type = "none"
        }
    }

     viewer_certificate {
        cloudfront_default_certificate = true

    } 
}
