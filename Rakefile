# Add your own tasks in files placed in lib/tasks ending in .rake,
# for example lib/tasks/capistrano.rake, and they will automatically be available to Rake.

require_relative 'config/application'
require "mini_magick"
require 'uri'

Rails.application.load_tasks

desc "read file"

  task :readfile  => :environment  do
    #берем посты
    posts = File.read"./db/wp_posts.json"
    post_data = JSON.parse(posts)
    post_data.each do |post|

      if post["post_content"].present? && post["post_title"].present? && post["post_status"] === "publish" && post["post_parent"] === '0'
         short = post["post_title"].slice(0, 150).gsub(/<[a-zA-Z\/][^>]*>/, '')

         Article.create(
            body: post["post_content"].gsub(/\[[^\]]*\]/, ''),
            title: post["post_title"], publish_on: Time.zone.now,
            search: post["ID"], shorttext: short,
            author: "old.medach.pro"
            )
      else
        puts post["ID"] + " " + "НЕ ПОЛНЫЙ"
      end
    end
  end

  task :image => :environment do      
    posts = File.read"./db/wp_posts.json"
    post_data = JSON.parse(posts)
    post_data.each do |post|

      if post["post_type"] === "attachment" && post["post_parent"] != '0'
         
        kek = Article.find_by(search: post["post_parent"])
        unless kek.nil?
          kek.update(remote_image_url: post["guid"])
        else
          puts "nil"
        end
        
      else
        puts "jopa"
      end

    end
  end


desc "add tags on articles"
  task :addtags  => :environment  do
      #берем теги
      tags = File.read"./db/wp_terms.json"
      tags_data = JSON.parse(tags)
      #берем таблицу связей тегов с постами
      tags_r = File.read"./db/wp_term_relationships.json"
      tags_r_data = JSON.parse(tags_r)
  
      tags_data.each do |tag|
        tags_r_data.each do |taggin|
      
        if tag["term_id"] === taggin["term_taxonomy_id"]
          lolik = Article.find_by(search: taggin["object_id"])
          unless lolik.nil?
            lolik.tag_list.add(tag["name"])
            lolik.save
            puts "NORMUL"
          else
            puts "nil"
          end
        else 
          puts "error"
        end
      end
    end
  end
