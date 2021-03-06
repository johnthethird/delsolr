== DO NOT USE! Specific hacks for a client app. The main gist being using the tag and ex lucene commands to
do multi-valued faceting. One of these days we will productize it for general consumption, with the requiste blog post.

= delsolr

http://delsolr.rubyforge.org

== DESCRIPTION:

DelSolr is a light weight ruby wrapper for solr.  It's intention is to expose the full power of solr queries
while keeping the interface as ruby-esque as possible.

== Installation

Can be installed as a gem which is hosted on http://gemcutter.org

    gem install delsolr

or as a plugin...

    ruby script/plugin install git://github.com/avvo/delsolr.git


== PROBLEMS:

* Not threadsafe yet

== SYNOPSIS:

See http://delsolr.rubyforge.org for more info

Example:

    c = DelSolr::Client.new(:server => 'solr1', :port => 8983)
    rsp = c.query('dismax', :query => 'mp3 player',
                            :filters => {:cost => (50..100), :localparams => {:tag => 'cost_filter'}},
                            :facets => [{:field => 'brand', :limit => 10},
                                        {:query => {:onsale => true, :brand => 'Apple'},
                                         :localparams => {:key => 'cheap_apple', :ex => 'cost_filter'}}])

    # output total matches
    puts rsp.total

    # output each id with score
    rsp.docs.each { |doc| puts "#{doc[:id]} - #{doc[:score]}" }

    # output each value for a facet
    rsp.facet_field_values('brand').each do |brand|
      puts "#{brand}: #{rsp.facet_field_count('brand', brand}"
    end

    # output a query facet
    puts "Cheap Apple stuff: #{rsp.facet_query_count_by_key('cheap_apple')}"

    # adding things
    doc = DelSolr::Document.new
    doc.add_field('id', 'ABC-123')
    doc.add_field('name', '8 Gig Shuffle')
    doc.add_field('description', 'A crazy-tiny MP3 player')
    c.update!(doc) # posts new document to solr
    c.commit! # posts a commit to solr

    rsp = c.query('dismax', :query => 'shuffle mp3 player')
    puts rsp.ids[0]

	# using local params for filters / facets
	Use :localparams => {} syntax to add local params to filters and facets


== REQUIREMENTS:

You need Solr(1.4 or higher) installed somewhere so you can query it ;)

== INSTALL:

sudo gem install delsolr

== TODO:

 * implement delete_by_query
 * make thread safe
   * it would be nice to be able to have things like commit/optimize be ran in threads on timers periodically
   * right now a few things need to be locked
     * connection
     * pending_documents array


== LICENSE:

(The MIT License)

Copyright (c) 2008 Avvo, INC - http://www.avvo.com

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
