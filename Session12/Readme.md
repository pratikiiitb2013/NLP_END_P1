# Following is my understanding for different blocks of code:
--------------------------------------------------------------

# Encoder
* we take input and pass through embedding layer to get input embeddings.
* Then we also create positional encodings and add to input embeddings.
* position values are created by aranging from 0 to src_len for batch size times. These values are passed to position encodings to get embeddings.
* All the processing inside the encoder( to create multi head attention mechanism) can be called N times. So we define this as Encoder layer which we can call N times.
* We need to mask some of the input also. Because we don't want pad tokens to go inside multi-head attention network as we don't want softmax values to be calculated around pad tokens but only for the real data tokens.
So we are handling this by making a mask.


# Encoder Layer
* Encoder layer is the main processing unit for encoder where most of the things will be happening.
* It will need src and src_mask as input.
* There are 2 different types of attention layers, one which is used in Encoder layer, is self_attention layer. So we will be needing that here. So we will create an object of class MultiHeadAttentionLayer here.
* MultiHeadAttentionLayer class is seperately defined and which is for self_attention.
*Now this self_attention needs 4 inputs, 3 times src and lastly src_mask. 3 times src represents query, key and value concepts.
* We called self_attention which gives processed output and attention as output.
* Now, self_attention part, just take care of the attention part.
* After this we add this output to the residual(input before sending to attention layer) and normalize.
* After this, we need to pass the input to feed forward layer. Again like self_attention, we define a seperate class for this feed forward layer.
* More description about feed forward layer can be found below.
* After feed forward layer returns output, we again adding a residual connection to this output and normalizing the added output.
* Finally, we will return this output of 2nd normalised part and this will go as an input to attention part of decoder.

# MultiHeadAttentionLayer explaination
* We got query, key and value as input.
* We will also need as input to class, how many heads we want to split the input into.
* Now, before splitting into multiple heds we are going to pass query, key and value vectors to FC layers so to arrange the similar concepts near by, so they remain in same blocks( when we divide into multiple blocks according to heads).
* After dividing into heads, for each head, we are going to multiple query to key.
* This multiplication is the energy tensor.
* We process energy by mask earlier passed as input.
* Then we calculate attention bby taking softmaz values of enegy vector.
* Then, we multiple attention to value vector.
* Finally, we need to re arrange(through permute) and concatenate data for different heads together and pass through a final output FC layer and return the output.

# PositionwiseFeedforwardLayer explaination
* The input is going to be transformed from hidden dimension to some other dimension which is lot larger than hidden dimension.
* And then back again from larger dimension to hidden dimension.
* This is done to increase the capacity of the dimensions so that more information can be processed.

# Decoder
* Decoder part is very similar to encoder part.
* Instead of just one self attention like in encoder, it uses 2 multi-head attentions, one self attention with all Q, K and V same. And another attention where Q is different while K and V are cming from encoder.
* The working is explained below where we make another class for decoder layer.

# Decoder Layer
* It starts with one self attention layer, where Q, K and V are same and that is target embeddings.
* After that, similar to encoder, we add residual connection to output of self_attention and normalize it.
* After that, this is passed to another attention layer which is called encoder attention layer. Here Q is now target, while K and V are changed now. They are coming from encoder.
* Again, we add a residual connection to last output and normalize.
* Now, similarly as in encoder, we send to feed forward network and then add residual and normalize.
* Now return the last output along with attention.

