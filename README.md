#Define S3 id
resource "aws_s3_bucket" "b1" {
bucket = "random-vik-12
acl = "public-read"
region = "us-east-1"


versioning {
enabled = true 
}

tags = {
name = "random-vik-12"
environment = "prod"
}

provisioner "local-exec" {
command = "git clone https://github.com/vikaSRK75/terraform #UPLOAD CONTAIN HERE
}
}


#allow public access

resource "aws_s3_bucket_access_block" "public_storage"
depends_on = [aws_s3_bucket_random-vik-12]
bucket = "random-vik-12"
block_public_acls = false

}

#upload data

resource "aws_s3_bucket_object" "object1" {
depends_on = [aws_s3_bucket_random-vik-12]
bucket = "random-vik-12"
acl = "public-read"
key = "profile"

}

#cloud front 

resource "aws_cloudfront_distribution" "terra-cloudfront1" {
depends_on = [aws_s3_bucket_random-vik-12]

origin {
domain_name = aws_s3_bucket_random-vik-12.bucket.
origin_id = local.s3_origin_id.
}

enabled = true 
default_cache_behavior {
allowed_methods  = ["DELETE", "GET", "HEAD", "OPTIONS", "PATCH", "POST", "PUT"]
cached_methods   = ["GET", "HEAD"]
target_origin_id = local.s3_origin_id

forwarded_values {
query_string = false

cookies {
forward = "none"
 }
  }

viewer_protocol_policy = "allow-all"
min_ttl                = 0
default_ttl            = 3600
max_ttl                = 86400
  }

restrications {
geo_restricatios {
restrications_type = "none"
}
}

viewer_certificate {
cloudfront_default_certificate = true
  }
}
