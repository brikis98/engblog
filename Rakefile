public_dir = "public"
source_dir = "source"
deploy_dir = "_deploy"
posts_dir = "_posts"
projects_dir = "_projects"
bios_dir = "_bios"
stash_dir = "source/_stash"

git_url = "git@github.com:thinkdeciduous/engblog.git"
git_branch = "gh-pages"
git_master = "master"

desc "Install Jekyll and all it's dependencies."
task :install do
	begin
		require "jekyll"
		require "redcarpet"
		require "launchy"
		abort("Already installed all dependencies! Run \"rake deploy\" if you have not already to initialize the authoring environment")
	rescue LoadError
		puts "Installing Ruby dependencies..."
		system "gem install jekyll"
		system "gem install redcarpet"
		system "gem install launchy"
		system "mkdir public"
	end
end

desc "Setup the git-pages deployment area and initialize the repo for blog commits"
task :deploy do
	abort("Blog environment has already been deployed!") if File.directory?(deploy_dir)
	puts "Initializing authoring environment..."	
	system "mkdir #{deploy_dir}"
	cd "#{deploy_dir}" do
		# this all needs to be changed once I get the LinkedIn repo setup
		system "git init"
		system "git remote add origin #{git_url}"
		system "git pull origin"
		system "git checkout gh-pages"
	end
end

desc "Move all other posts than the one currently being worked on to a temporary stash location so regenerating the site happens much more quickly."
task :isolate, :filename do |t, args|
  FileUtils.mkdir("#{source_dir}/#{stash_dir}/") unless File.exist?("#{source_dir}/#{stash_dir}/")
  Dir.glob("#{source_dir}/#{posts_dir}/*.*") do |post|
    FileUtils.mv post, "#{source_dir}/#{stash_dir}/" unless post.include?(args.filename)
  end
end

desc "Move all stashed posts back into the posts directory, ready for site generation."
task :integrate do
  FileUtils.mv Dir.glob("#{source_dir}/#{stash_dir}/*.*"), "#{source_dir}/#{posts_dir}/"
end

desc "Generate all static content with Jekyll and commit blog changes to github"
task :push do
	system "jekyll build"
	cd "#{deploy_dir}" do
		system "git pull"
	end
	#(Dir["#{deploy_dir}/*"]).each { |f| rm_rf(f) }
	cp_r "#{public_dir}/.", deploy_dir
	cd "#{deploy_dir}" do
		system "git add -A"
		message = "Site updated at #{Time.now.utc}"
		system "git commit -m \"#{message}\""
		system "git push origin #{git_branch}"
	end
	cd "#{source_dir}/#{posts_dir}" do
		system "git add -A"
		message = "Source upodated at #{Time.now.utc}"
		system "git commit -m \"#{message}\""
		system "git push origin #{git_master}"
	end
end

desc "Generate a temporary site and launch the Jekyll server"
task :preview do 
	require "launchy"
	cd "#{deploy_dir}" do
		Thread.new do
			sleep 4
			puts "Opening preview site in browser..."
			Launchy.open("http://localhost:4000")
		end
		puts "Generating preview site..."
		system "jekyll serve --watch"
		rm_rf "_site"
	end
end

